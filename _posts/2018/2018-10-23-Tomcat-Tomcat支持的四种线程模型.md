---
layout:     post             				# 使用的布局（不需要改）
title:         Tomcat支持的四种线程模型    # 标题 
subtitle:    					  				#副标题
date:       2018-10-27  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - Java Basics
    - 编程之路
    - 自我总结
---

| 名称 | 描述 |
| ------ | ------ |
| BIO | 阻塞式IO，采用传统的java IO进行操作，该模式下每个请求都会创建一个线程，适用于并发量小的场景 |
| NIO | 同步非阻塞，比传统BIO能更好的支持大并发，tomcat 8.0 后默认采用该模式 |
| APR | tomcat 以JNI形式调用http服务器的核心动态链接库来处理文件读取或网络传输操作，需要编译安装APR库 |
| AIO | 异步非阻塞，tomcat8.0后支持 |

``` xml
配置方法：在tomcat conf 下找到server.xml

在<Connector port="8080" protocol="HTTP/1.1"/>

BIO:  protocol =" org.apache.coyote.http11.Http11Protocol"

NIO: protocol ="org.apache.coyote.http11.Http11NioProtocol"

AIO: protocol ="org.apache.coyote.http11.Http11Nio2Protocol"

APR: protocol ="org.apache.coyote.http11.Http11AprProtocol"
```

####  BIO线程模型：
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-BIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B.png)

1. Http11Protocol组件，是HTTP协议1.1 版本的抽象，它包含客户端连接、接收客户端消息报文、报文解析处理、对客户端响应等整个过程。它主要包含JIoEndpoint组件和Http11Processor,启动时，JIoEndpoint组件内部的Acceptor组件将启动某个端口的监听，一个请求到来后将被扔进线程池Executor，线程池进行任务处理处理，处理过程中将通过Http11Processor组件对HTTP协议解析并传递到Engine容器继续处理。

2. Mapper组件，可以通过请求地址找到对应的servlet

3. CoyoteAdapter组件，将一个Connect和Container适配起来的适配器。

#### JIoEndpoint组件
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B61.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B62.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B63.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B64.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B66.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B67.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-JIoEndpoint%E7%BB%84%E4%BB%B68.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-102825.png)

#### NIO线程模型：

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-NIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B1.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-NIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B2.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-NIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B3.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-NIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B4.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-NIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B5.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-NIO%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%9E%8B6.png)




