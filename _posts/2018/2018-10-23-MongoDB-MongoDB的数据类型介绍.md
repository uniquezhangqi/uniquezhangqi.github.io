---
layout:     post             				# 使用的布局（不需要改）
title:         MongoDB的数据类型介绍    # 标题 
subtitle:    					  				#副标题
date:       2018-10-27  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - DB
    - 编程之路
    - 自我总结
---

> MongoDB官网：https://docs.mongodb.com/manual/reference/bson-types/

MongoDB文档存储是使用BSON类型，BSON（BSON short for Bin­ary JSON, is a bin­ary-en­coded seri­al­iz­a­tion of JSON-like doc­u­ments）是二进制序列化的形式

## 比较常用的几个类型
#### ObjectId类型：
这是MongoDB生成的类似关系型DB表主键的唯一key，生成快速。具体由12个字节组成：
前4个字节是unix秒，3个字节的机器标识符（为了分布式下的主键唯一），2个字节的进程id，3个字节的计数器数字


MongoDB的设计之初就是要做分布式数据库。从ObjectId唯一主键的生成上，值得分布式系统设计人员参考。


3个字节的机器标识符，表示MongoDB实例所在机器的不同；2个字节的进程id，表示相同机器的不同MongoDB实例。再加上时间戳和随机数（3个字节随机数，同一秒上，理论上可以有2^24次个插入），很大程度上保证了ObjectId的唯一性。
　　
#### String类型：　　
BSON字符串都是UTF-8编码。

#### Timestamps类型：
BSON具有内部MongoDB使用的特殊时间戳类型，并且不与常规Date类型相关联。 时间戳值是64位值，其中：

- 第一个32bit是unix时间戳秒；
- 第二个32bit是当前秒的递增操作数。
- 可以保证一个mongod实例下，timestamps总是唯一的。

#### Date类型：
BSON Date是一个64bit有符号整数，表示自Unix纪元以来的毫秒数（1970年1月1日）。 






