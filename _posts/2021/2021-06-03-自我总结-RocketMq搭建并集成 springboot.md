---
layout:     post             				# 使用的布局（不需要改）
title:         RocketMq搭建并集成 springboot     # 标题 
subtitle:    					  				#副标题
date:       2021-06-04  					# 时间
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
---

&nbsp;
&nbsp;

## 前言叨逼叨

- 如何安装.
- 集成 springboot 并把 同步、异步、批量、延时、事务消息都跑一遍.
- 配合机械工业出版社《RocketMQ实战与原理解析》perfect ！ 我一下午就看完了.

![](https://tva1.sinaimg.cn/large/008i3skNgy1gr782j1o9zj30u01404qp.jpg)


## 安装

rocketmq官方文档：
https://github.com/apache/rocketmq/tree/master/docs/cn

下载地址：
https://rocketmq.apache.org/docs/quick-start/

![](https://tva1.sinaimg.cn/large/008i3skNgy1gr6fybl03bj31ew0matf6.jpg)

上面截图这个界面后，下面有下载下来后怎么安装，怎么启动。

```java

  > unzip rocketmq-all-4.8.0-source-release.zip
  > cd rocketmq-all-4.8.0/
  > mvn -Prelease-all -DskipTests clean install -U
  > cd distribution/target/rocketmq-4.8.0/rocketmq-4.8.0

```

## 图形化界面

图形化管理控制台：
git clone -b release-rocketmq-console-1.0.0 
https://github.com/apache/rocketmq-externals.git

对应文档：
https://github.com/apache/rocketmq-externals/blob/master/rocketmq-console/doc/1_0_0/UserGuide_CN.md

完成后界面：

![web1](https://tva1.sinaimg.cn/large/008i3skNgy1gr772ujj90j31gx0pugpy.jpg)

![web2](https://tva1.sinaimg.cn/large/008i3skNgy1gr77398nz0j31h20qb0vo.jpg)

![web3](https://tva1.sinaimg.cn/large/008i3skNgy1gr773jiefnj31gx070ab8.jpg)

![web4](https://tva1.sinaimg.cn/large/008i3skNgy1gr773rnzajj31gj0p3gqc.jpg)

![web5](https://tva1.sinaimg.cn/large/008i3skNgy1gr773xplrmj31h60qb43n.jpg)

![web6](https://tva1.sinaimg.cn/large/008i3skNgy1gr7745b4g1j31gu0q841e.jpg)


## 内存不足启动失败修改JVM 参数

```java
➜  bin pwd
/Users/ian/rocketmq-all-4.8.0-source-release/distribution/bin
```

![ bin vim runbroker.sh](https://tva1.sinaimg.cn/large/008i3skNgy1gr6ra00fy8j324w0h0wos.jpg)

![bin vim runserver.sh](https://tva1.sinaimg.cn/large/008i3skNgy1gr6rb71d5lj319m0dqdl2.jpg)

![集群配置](https://tva1.sinaimg.cn/large/008i3skNgy1gr7705y6zmj62300aygr402.jpg)

|多Master多Slave模式（异步）|多Master多Slave模式（同步）|多Master模式|
|:--:|:--:|:--:|
|2m-2s-async|         2m-2s-sync  |        2m-noslave|



## 集成

GitHub:
https://github.com/apache/rocketmq-spring

这个工程下面的 rocketmq-spring-boot-samples 示例
![](https://tva1.sinaimg.cn/large/008i3skNgy1gr77i6fy7kj30gu06kjs0.jpg)

```xml
<!--add dependency in pom.xml-->
<dependency>
    <groupId>org.apache.rocketmq</groupId>
    <artifactId>rocketmq-spring-boot-starter</artifactId>
    <version>${RELEASE.VERSION}</version>
</dependency>
```

支持：

- 同步发送消息
- 异步发送消息
- 以单向模式发送消息
- 发送有序消息
- 发送批量消息
- 发送交易消息
- 发送具有延迟级别的预定消息
- 以并发模式（广播/集群）消费消息
- 消费有序消息
- 使用标记或 sql92 表达式过滤消息
- 支持消息追踪
- 支持认证和授权
- 支持请求-回复消息交换模式
- 使用推/拉模式消费消息

![consumer1](https://tva1.sinaimg.cn/large/008i3skNgy1gr787ov4ycj31n20u0to3.jpg)

![consumer2](https://tva1.sinaimg.cn/large/008i3skNgy1gr7887rszyj31mx0u0tog.jpg)

![consumer3](https://tva1.sinaimg.cn/large/008i3skNgy1gr788ks4sqj31mi0u0wsm.jpg)

![producer1](https://tva1.sinaimg.cn/large/008i3skNgy1gr788tl0utj31n50u0ws3.jpg)

![producer2](https://tva1.sinaimg.cn/large/008i3skNgy1gr7892nrzsj31kf0u0anz.jpg)

![producer3](https://tva1.sinaimg.cn/large/008i3skNgy1gr789c31esj31ri0dgq6j.jpg)

## 发现 mac 有自带压缩图片

![Mac 自带拉动滑条压缩图片](https://tva1.sinaimg.cn/large/008i3skNgy1gr78495803j31ba0oudlc.jpg)


## 参考

MAC上安装rocketmq磁盘空间不足的问题：
https://blog.csdn.net/dmsdr/article/details/54710368

RocketMQ踩坑记：
https://www.cnblogs.com/2YSP/p/11616376.html

rocketmq消息中台：
https://github.com/sohutv/mqcloud

阿里云springboot集成：
https://github.com/ThierrySquirrel/rocketmq-spring-boot-starter
