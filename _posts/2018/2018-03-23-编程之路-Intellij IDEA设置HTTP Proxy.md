---
layout:     post             				# 使用的布局（不需要改）
title:      Intellij IDEA设置HTTP Proxy          			# 标题 
subtitle:    					  				#副标题
date:       2018-03-23  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-re-vs-ng2.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - 编程之路
---

## Intellij IDEA设置HTTP Proxy
#### MAC版：
打开`Appearance & Behavior `> `System Settings` > `Http Proxy`
点选 `Auto-detect proxy settings` > `Automatic proxy configuration URL`
输入URL: `http://127.0.0.1:1080`即可。
如图：
![](https://ws1.sinaimg.cn/large/006tKfTcgy1fpm38fsnjcj31kw0zpdhc.jpg)

#### win pc:
输入URL: `http://127.0.0.1:1080/pac`即可。


#### 可以试试:
选第三个`Manual proxy configuration`<br>
类型选`HTTP`<br>
Host name：`127.0.0.1`<br>
Port number：`1080`

####参考：
<https://www.jianshu.com/p/a0ec13a34a4b>




