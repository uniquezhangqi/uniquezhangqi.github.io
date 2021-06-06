---
layout:     post             				# 使用的布局（不需要改）
title:        CentOS服务器 java 进程消失     # 标题 
subtitle:    					  				#副标题
date:       2021-05-30  					# 时间
author:     阿琦                  			# 作者
header-img: img/home-bg-o.jpg 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
istop:  false                             # 是否置顶
iscopyright: true                      # 是否版权，默认有
music-id:                                        # 网易云音乐单曲嵌入
music-idfull:                               # 网易云音乐歌单嵌入
apserver:                           # 音乐平台netease/tencent/kugou/xiami/baidu
aptype:     	           		# 音乐类型song/playlist/album/search/artist
apsongid:                    # 音乐song/playlist/album id
tags:                              	           	#标签
    - 自我总结
    - 运维
    - JVM
    - 问题定位
---

&nbsp;
&nbsp;


## 现象

- 每隔一段时间 netstat -ntpl 所有 java 进程全部消失
- 项目日志无报错

## 常规排查
`jstat -gc pid`、`jstat -gcutil pid`  分别是young gc的次数、young gc的时间、full gc的次数、full gc的时间gc的总时间。

```java
[root@iZj6cces3mggq80hc9a4srZ ~]# jstat -gcutil 1894975
Picked up JAVA_TOOL_OPTIONS: -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/aqi/logs/
  S0     S1     E      O      M     CCS    YGC     YGCT    FGC    FGCT     GCT
 79.60   0.00  74.71   3.12  95.60  93.61     14    0.499     6    0.127    0.626
```


`free -h -s 3` 持续的观察内存的状况，此时可以使用 -s 选项并指定间隔的秒数。
```
[root@iZj6cces3mggq80hc9a4srZ ~]# free -h -s 3
              total        used        free      shared  buff/cache   available
Mem:          7.4Gi       5.3Gi       1.4Gi       1.0Mi       785Mi       1.9Gi
Swap:            0B          0B          0B

              total        used        free      shared  buff/cache   available
Mem:          7.4Gi       5.3Gi       1.4Gi       1.0Mi       785Mi       1.9Gi
Swap:            0B          0B          0B
```

`df -hl -h` 查看磁盘剩余空间使用df -hl命令：

```
[root@iZj6cces3mggq80hc9a4srZ ~]# df -hl -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        3.8G     0  3.8G   0% /dev
tmpfs           3.8G     0  3.8G   0% /dev/shm
tmpfs           3.8G  500K  3.8G   1% /run
tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/vda1        40G  9.3G   31G  24% /
tmpfs           763M     0  763M   0% /run/user/0
```

`iostat`  iostat 输出磁盘IO 和 CPU的统计信息。

```
[root@iZj6cces3mggq80hc9a4srZ ~]# iostat
Linux 4.18.0-240.22.1.el8_3.x86_64 (iZj6cces3mggq80hc9a4srZ) 	05/30/2021 	_x86_64_	(2 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
          19.00    0.01    1.30    0.01    0.00   79.68

Device             tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
vda               1.94        26.69        32.39   21239674   25781653
```

`top`  动态地持续监听进程地运行状态。

1. 记录系统整体资源使用情况，进程信息、线程信息
top -b -n 3 > top_process.txt
2. 线程统计
top -H -b -n 3 > top_thread.txt
top -H -p 3342810
3. 保存tomcat/java 进程状态信息
pidstat -p 20157 1 3 -u -t >pidstat.txt
pidstat -p 3342810 1 3 -u -t
4. 保存线程堆栈信息
jstack -l 20157 -> jstack.txt

上面 1、2、3、4  用scp 下载本地排查。


详细介绍请看：[线上问题定位常用命令](https://mp.weixin.qq.com/s?__biz=Mzg5NjU1NDIxNQ==&mid=2247484192&idx=1&sn=1d95c49d26a3d7e9169b461e3914b671&chksm=c07e06a8f7098fbe56c7220b78526fce354c8b0211a232105e866123517621a68cfe5e0474fd&scene=27)


`jmap -dump:live,format=b,file=/tmp/xxxx.hprof pid` 导出dump ,用 jprofiler 打开分析.。

![](https://tva1.sinaimg.cn/large/008i3skNgy1gr0jrpkduqj317m0dsgou.jpg)

一顿操作猛如虎，全是正常的... ..

## 看本地能不能重现
本地 macos 11.4 系统，nohup java -jar xxx.jar &  正常，无法重现。


## 找到原因 OOM-KILL

`/var/log/messages` -  #系统报错日志，系统启动时的状态信息，运行时的状态信息。
下载到本地  也是正常，没问题。

`/var/log/syslog` - 我这个服务器默认没有，问了下谷歌要配置，先不管。

`dmesg` 查看系统各进程资源占用情况 ，然后就发现被系统杀掉了.... ..
![](https://tva1.sinaimg.cn/large/008i3skNgy1gr0k3823lvj31ws0f21kx.jpg)

![](https://tva1.sinaimg.cn/large/008i3skNgy1gr0k3sfukyj31xq0fq4qq.jpg)

## 检查jvm 

###  JVM自身故障
当JVM发生致命错误导致崩溃时，会生成一个`hs_err_pid_xxx.log`这样的文件，该文件包含了导致 JVM crash 的重要信息，我们可以通过分析该文件定位到导致 JVM Crash 的原因，从而修复保证系统稳定。

默认情况下，该文件是生成在工作目录下的，当然也可以通过 JVM 参数指定生成路径：
```
-XX:ErrorFile=/var/log/hs_err_pid<pid>.log
```
这个文件的内容他主要有如下内容:

- 日志头文件
- 导致 crash 的线程信息
- 所有线程信息
- 安全点和锁信息
- 堆信息
- 本地代码缓存
- 编译事件
- gc 相关记录
- jvm 内存映射
- jvm 启动参数
- 服务器信息

### 加启动参数

`-XX:+HeapDumpOnOutOfMemoryError` 参数表示当JVM发生OOM时，自动生成DUMP文件

`-XX:HeapDumpPath=${目录}` 参数表示生成dump 文件的路径，也可以指定文件名称

`-XX:+PrintGCDetails`  打印详细GC，这个参数我们上面例子已经用过了。

`-XX:+PrintGCDateStamps`  打印 GC 时间

``` java
// JAVA_TOOL_OPTIONS：是标准的，所有虚拟机都能识别和应用的。
[root@iZj6cces3mggq80hc9a4srZ ~]# vim /etc/profile
export JAVA_TOOL_OPTIONS="-Xloggc:/home/aqi/logs/gc.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/aqi/logs/java.hpro"
```

```java
// 生效后会输入 Picked up 
[root@iZj6cces3mggq80hc9a4srZ ~]# source /etc/profile
[root@iZj6cces3mggq80hc9a4srZ aqi]# java -version
Picked up JAVA_TOOL_OPTIONS: -Xloggc:/home/aqi/logs/gc.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/home/aqi/logs/java.hprof
openjdk version "1.8.0_282"
OpenJDK Runtime Environment (build 1.8.0_282-root_2021_01_21_17_34-b00)
OpenJDK 64-Bit Server VM (build 25.282-b00, mixed mode)
```

`jcmd 9142 VM.flags`  获取启动参数

```java
➜  target git:(master) ✗ ps -ef|grep java
  501  9142  7432   0  3:40PM ttys000    0:42.05 java -Xms1g -Xmx1g -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+UseCMSCompactAtFullCollection -XX:CMSInitiatingOccupancyFraction=75 -jar /Users/ian/work/wallet-filcoin/filecoin/target/filecoin-0.0.1-SNAPSHOT.jar
  501  9270  9077   0  3:42PM ttys003    0:00.00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox java

➜  target git:(master) ✗ jcmd 9142 VM.flags
9142:
-XX:CICompilerCount=4 -XX:CMSInitiatingOccupancyFraction=75 -XX:InitialHeapSize=1073741824 -XX:MaxHeapSize=1073741824 -XX:MaxNewSize=357892096 -XX:MaxTenuringThreshold=6 -XX:MinHeapDeltaBytes=196608 -XX:NewSize=357892096 -XX:OldPLABSize=16 -XX:OldSize=715849728 -XX:+UseCMSCompactAtFullCollection -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseConcMarkSweepGC -XX:+UseFastUnorderedTimeStamps -XX:+UseParNewGC
```

## 观察一段时间再看结果
之后JVM 和 java项目都正常了，ELK7.6.2 默认的 -Xms：初始堆大小、-Xmx：最大堆大小好像都是 1g，后面也全部都设置512m了。

JVM参数说明在参考的 **JVM日志参数十全大补丸** 里面有详细解释。



## 参考
JVM日志参数十全大补丸:
https://mp.weixin.qq.com/s/XZFEgf1ZS7gNt7lku3TF4g

Linux服务器Java进程突然消失排查办法:
https://blog.csdn.net/wtopps/article/details/88640948

CentOS系统日志:
https://www.cnblogs.com/linuxws/p/9020487.html

CENTOS下如何根据DUMP文件分析线上问题:
https://www.cnblogs.com/linus-tan/p/9328004.html

Java后端线上问题排查常用命令收藏:
https://mp.weixin.qq.com/s/7cInYxzRfQ-c7wKQBlbFXg#b960249c-911f-d2e3-e35a-9c55c090ad45

