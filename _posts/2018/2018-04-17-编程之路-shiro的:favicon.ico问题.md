---
layout:     post             				# 使用的布局（不需要改）
title:      shiro的/favicon.ico问题          # 标题 
subtitle:   ✍🏻 					  				#副标题
date:       2018-04-17  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-re-vs-ng2.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - 编程之路
    - 鉴权
---



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



