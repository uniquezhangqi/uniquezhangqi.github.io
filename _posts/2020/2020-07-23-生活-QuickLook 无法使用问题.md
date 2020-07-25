---
layout:     post             				# 使用的布局（不需要改）
title:        QuickLook 无法使用问题    # 标题 
subtitle:    转载 JoyLau				  				#副标题
date:       2020-07-23 					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 随笔
    - 杂谈
    - 生活
---


![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh1au68mzmj30wu0f875v.jpg)


## 解决方案一

删除 ~/Library/QuickLook 目录下的隔离属性 (quarantine attribute)

```xml
// 查看
xattr -r ~/Library/QuickLook

// 移除
xattr -d -r com.apple.quarantine ~/Library/QuickLook
```


## 解决方案二

空格预览文件出现下列提示,点击 取消

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh1avwlpmij30wm0f6abk.jpg)

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh1awbb2yvj30z60u0767.jpg)

点击 “Allow Anyway”

使用下列命令打开刚才需要预览的文件

```xml
    qlmanage -p /path/to/any/file.js
```

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gh1ayh4vmoj30xa0fsmz0.jpg)

然后就可以预览该后缀名的所有文件了

如果需要预览其他类型的文件,则将上述步骤重新操作一遍, 换个后缀名即可.

