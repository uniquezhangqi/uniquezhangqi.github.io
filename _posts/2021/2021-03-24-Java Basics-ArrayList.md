---
layout:     post             				# 使用的布局（不需要改）
title:        ArrayList 源码总结   	 # 标题 
subtitle:    					  				#副标题
date:       2021-03-24  					# 时间
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
    - Java Basics
    - 自我总结
---

&nbsp;
&nbsp;

## ArrayList
与它类似的是LinkedList，和LinkedList相比，它的查找和访问元素的速度较快，但新增，删除的速度较慢。

小结：ArrayList底层是用数组Object[] elementData实现的存储。

特点：查询效率高，增删效率低，线程不安全。使用频率很高。

## ArrayList 底层数组大小
### 无参构造函数

![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzmycdaqj30tk08kab9.jpg)

源码注释：`Constructs an empty list with an initial capacity of ten.`
构造一个初始容量为10的空列表。

![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzndkyz8j30x00a4jti.jpg)

### 有参构造函数

初始化的时候指定底层数组的大小
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplznmbx40j31120lajwd.jpg)

## ArrayList 新增

add(E e) 新增会扩容
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzny7sjsj316k0dak5i.jpg)

![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzoaz9vyj313m0gq1bm.jpg)

add(int index, E element)  在指定位置插入指定元素
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzoinfjcj30x20ksjvu.jpg)

拷贝数组： 它是复制了一个数组，从index(传进来的，如果是 6，那么就是 index 6) 的位置开始的，然后把它放在了index(传进来的, 如果是 6，那么就是 6+1 ) +1的位置。腾出来的位置给新增的元素。
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzoocn8vj315a0mw4qp.jpg)


## ArrayList 删除
remove(int index);  和新增一样也是拷贝数组，删除的位置被覆盖了。
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzow2ywdj315a0qohbr.jpg)


## 为什么ArrayList要比LinkedList快得多
论遍历ArrayList要比LinkedList快得多，ArrayList遍历最大的优势在于内存的连续性，CPU的内部缓存结构会缓存连续的内存片段，可以大幅降低读取内存的性能开销。


## 初始化数组大小

ArrayList(int initialCapacity) ；

不会初始化数组大小。
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzp2zhz3j314s0lqagl.jpg)

初始化后 size 还是 0.
![](https://tva1.sinaimg.cn/large/008eGmZEly1gplzp9q6rjj30yw0igaek.jpg)


