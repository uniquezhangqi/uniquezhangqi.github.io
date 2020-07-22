---
layout:     post             				# 使用的布局（不需要改）
title:      shiro的/favicon.ico问题          # 标题 
subtitle:   ✍🏻 					  				#副标题
date:       2018-04-17  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-re-vs-ng2.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - shiro
---

> 本文首次发布于[My Blog](http://uniquezhangqi.top),作者[@张琦(Ian)](http://uniquezhangqi.top/about/),转载请保留原文链接。

　　最近Z 在搭建项目框架用到遇到shiro的这个问题，还好公司大佬告知，不然蒙蔽了😷
####登录成功之后跳转/favicon.ico问题。
 1. spring-shrio.xml里面配置加上
  - /favicon.ico = anon

 2. web.xml中配置中加上：
 
    ```xml
    <mime-mapping>
        <extension>ico</extension>
        <mime-type>image/x-icon</mime-type>
    </mime-mapping>
```



![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqj5aochgoj309k09kmwz.jpg)
<b><center>扫描关注：热爱生活的大叔</center>
<b><center><font size="2">（<font size="2" color="#FF0000">转载本站文章请注明作者和出处</font> <font size="2" color="#0000FF">热爱生活的大叔-uniquezhangqi</font><font size="2">）</font>