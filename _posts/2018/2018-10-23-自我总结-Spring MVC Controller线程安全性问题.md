---
layout:     post             				# 使用的布局（不需要改）
title:         Spring MVC Controller线程安全性问题	# 标题 
subtitle:    					  				#副标题
date:      2018-10-27  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
---

spring生成对象默认是单例（也就是一个对象）的。通过scope属性为prototype可以更改为多例。


由于只有一个Controller的实例，当多个线程同时调用它的时候，它的成员变量就`不是线程安全的`。 


当然在大多数情况下，我们根本不需要Controller考虑线程安全的问题，除非在类中声明了成员变量。因此Spring MVC的Contrller在编码时，尽量避免使用实例变量。如果一定要使用实例变量，则可以改用以下方式： 
1. Controller中声明 scope=”prototype”，即设置为多例模式 
2.在Controller中使用ThreadLocal变量,如：private ThreadLocal<Integer> count = new ThreadLocal<Integer>();






