---
layout:     post             				# 使用的布局（不需要改）
title:         搭建ElasticSearch 6.6.1  # 标题 
subtitle:    					  				#副标题
date:       2019-03-02  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
---

> 本文首次发布于[My Blog](http://uniquezhangqi.top),作者[@张琦(Ian)](http://uniquezhangqi.top/about/),转载请保留原文链接。


##  引言
虚拟机、服务器 系统请参考最下面的参考链接，下面安装只适用Mac book。

## 安装ES

- 使用HomeBrew包管理器 安装 brew install elasticsearch.
- 官方文档：https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html

### 启动：

- 到安装根目录下 sh ./bin/elasticsearch
- 或者 到bin目录下  直接 elasticsearch

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-03-02-es%20start.png)

### 踩坑
如果用root账号启动，是会报错的，网上搜索得知这个是ES的一个坑 ~


Mac 不能使用 groupadd 、useradd 创建普通用户 和 群组，需使用：
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-03-02-%E5%88%9B%E5%BB%BA%E8%B4%A6%E5%8F%B7.png)
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-03-02-%E6%99%AE%E9%80%9A%E8%B4%A6%E5%8F%B7.png)


#### 切换用户：
su - 用户名

```
例如： su - es
```

### 踩坑 
用root跟普通账号给指定目录权限：
`sudo chown -R es:staff elasticsearch`

chown -R 普通用户名(这里不是全名称): 群组 elasticsearch

只要是elasticsearch 的目录都需要给权限。

### 修改配置
head插件与ElasticSearch是两个独立的进程，解决它们之间的访问跨域问题，需要在elasticsearch.yml 最末端加入：
```
http.cors.enabled: true
http.cors.allow-origin: "*"
```

[![asciicast](https://asciinema.org/a/HgcuLjyNwMquu6Sx5opAQ8FIJ.svg)](https://asciinema.org/a/HgcuLjyNwMquu6Sx5opAQ8FIJ)


### 踩坑
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-03-02-%E6%9B%BF%E6%8D%A2%E6%96%87%E4%BB%B6%E5%90%8D-1.png)

截图里面的2个配置文件都需要跟自己的default配置文件相互修改对应的名字，不然会报错。
它这里是优先使用没有.default的配置文件。

```
例子： 
1. A   A.default     A 改为 A.其它   A.default 改为 A
2. B   B.default	 B 改为 B.其它   B.default 改为 B
```

### 可能会使用
查看：
ps -ef|grep elasticsearch

关闭：
kill -9 29617

## 安装head插件

GitHub：elasticsearch-head -- <https://github.com/mobz/elasticsearch-head>下载下来。

### 需要安装node：

- brew install node
- node -v
- 到 elasticsearch-head 根目录下第一次 npm install 再 npm run start ；
elasticsearch-head 没更新的话，第二次直接 npm run start 就好；
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-03-02-es-head%20start.png)

## 成功结果截图
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-03-02-html%20view.png)





> 参考1：https://juejin.im/post/59cb1cbf6fb9a00a3f24fc32#heading-10
> 
> 参考2：https://www.cnblogs.com/liuxiaoming123/p/8081883.html
> 
> 参考3：https://segmentfault.com/a/1190000015676868


文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！

