---
layout:     post             				# 使用的布局（不需要改）
title:      dubbo admin     # 标题 
subtitle:    					  				#副标题
date:       2019-02-17  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 分布式
---

> 参考 安装dubbo admin-- https://blog.csdn.net/javahighness/article/details/81230136


1. git clone https://github.com/apache/incubator-dubbo-ops
2. 配置路径：

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-02-17-dubbo-admin%E9%85%8D%E7%BD%AE%E8%B7%AF%E5%BE%84.png)

3. 修改配置 ：

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-02-17-Dubbo%20Admin%E9%85%8D%E7%BD%AE%E8%AF%B4%E6%98%8E.png)

4. 用maven 打成jar 然后用java -jar xxx.jar 启动，网页输入`http://localhost:端口号`成功结果截图：

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-02-20-new--dubbo-admin-UI.png)

dubbo-admin 和 需要监控的项目是各自独立的，dubbo-admin相当于一个插件之类。




文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！





