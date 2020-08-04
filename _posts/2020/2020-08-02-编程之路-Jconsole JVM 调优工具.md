---
layout:     post             				# 使用的布局（不需要改）
title:         Jconsole JVM 调优工具使用   # 标题 
subtitle:    			  				#副标题
date:       2020-08-02  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: false                      # 是否版权
music-id:                                        # 网易云音乐单曲嵌入
music-idfull:                               # 网易云音乐歌单嵌入
apserver:                           # 音乐平台netease/tencent/kugou/xiami/baidu
aptype:     	           		# 音乐类型song/playlist/album/search/artist
apsongid:                    # 音乐song/playlist/album id
tags:                              	           	#标签
    - jvm
    - 编程之路
---

&nbsp;
&nbsp;

> 转载：https://blog.csdn.net/shijing266/article/details/81511687

## JConsole查看当前程序/进程的全局情况


![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcp4gkh19j317g0deq6o.jpg)


![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcp6nwj11j313k0u0dig.jpg)



第一个是本地，第二个选项是远程连接


![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcpbfwm92j310l0u00uu.jpg)


概览 -- 分别是堆内存使用、线程、类、CPU 占用率




## 单独查看内存使用情况和GC回收情况：


![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcpf59za6j310r0u0goe.jpg)


垃圾回收GC，分为2种，一是Minor GC，可以可以称为YGC，即年轻代GC，当Eden区，还有一种称为Major GC，又称为FullGC


主要看新生代和老年区的内存使用情况，一般 YGC执行在Eden区，FullGC执行在老年区；

### GC原理：

> 参考：https://blog.csdn.net/Javazhoumou/article/details/99298624

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcsy2s2lrj30r00bqt9r.jpg)

我们可以看到年轻代包括Eden区(对象刚被new出来的时候，放到该区)，S0和S1，是幸存者1区和幸存者2区，从名字可以看出，是当发生YGC，没有被任何其他对象所引用的对象将会从内存中被清除，还被其他对象引用的则放到幸存者区。当发生多次YGC，在S0、S1区多次没有被清楚的对象，则会被移到老年代区域。当老年代区域被占满的时候，则会发送FullGC。

无论是YGC或是FullGC，都会导致stop-the-world，即整个程序停止一些事务的处理，只有GC进程允许以进行垃圾回收，因此如果垃圾回收时间较长，部分web或socket程序，当终端连接的时候会报connetTimeOut或readTimeOut异常，


从JVM调优的角度来看，我们应该尽量避免发生YGC或FullGC，或者使得YGC和FullGC的时间足够的短。





## 查看程序中线程的情况

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcptwt4qhj311v0u0acl.jpg)


可查看总线程数和各个线程的状态

## 单独查看程序中类的加载和卸载情况

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcpvaiadcj311z0u0q41.jpg)


当前程序加载的 class 和卸载的 class梳理


## 查看VM的概要情况以及相关运行参数

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1ghcpxve7gyj313h0u041y.jpg)


活动线程、峰值、守护线程、启动线程总数

已加载类、已加载类总数、已卸载类总数

总物理内存、闲置物理内存、总交换空间、闲置交换空间


> JVM的内存分布结构分析：<http://www.codeceo.com/article/jvm-memory-stack.html>