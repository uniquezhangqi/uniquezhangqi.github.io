---
layout:     post             				# 使用的布局（不需要改）
title:         日常总结 list 及lombok需注意     # 标题 
subtitle:    					  				#副标题
date:       2019-06-04					# 时间
author:     Ian                  			# 作者
header-img: img/ian-bg-red.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
---

> 本文首次发布于[My Blog](http://uniquezhangqi.top),作者[@张琦(Ian)](http://uniquezhangqi.top/about/),转载请保留原文链接。

## lombok 注意的问题
`@Data`相当于`@Getter` ,` @Setter` ,`@RequiredArgsConstructor`,` @ToString `,`@EqualsAndHashCode`这5个注解的合集。

## 问题

通过官方文档，可以得知，当使用@Data注解时，则有了@EqualsAndHashCode注解，那么就会在此类中存在equals(Object other) 和 hashCode()方法，且不会使用父类的属性，这就导致了可能的问题。 
比如，有多个类有相同的部分属性，把它们定义到父类中，恰好id（数据库主键）也在父类中，那么就会存在部分对象在比较时，它们并不相等，却因为lombok自动生成的equals(Object other) 和 hashCode()方法判定为相等，从而导致出错。

### 修复此问题的方法很简单： 
1. 使用@Getter @Setter @ToString代替@Data并且自定义equals(Object other) 和 hashCode()方法，比如有些类只需要判断主键id是否相等即足矣。 
2. 或者使用在使用@Data时同时加上@EqualsAndHashCode(callSuper=true)注解。
### 注意

当你的类没有显式继承其他类的时候，使用该注解会有编译异常。

```java
// 截取List集合中的一部分，组成新的List集合
        List<String> list = new ArrayList();
        list.add("1");
        list.add("2");
        list.add("3");
        list.add("4");
        list.add("5");
        list.add("6");
//        list.add("7");
        for (int i = 0; i < list.size(); i = i + 2) {
            if (i + 2 > list.size()) {
//                System.out.println(list.subList(i, list.size()));
                String num = list.subList(i, list.size()).toString();
                System.out.println(num);
            } else {
                String num = formatList(list.subList(i, i + 2),"&");
//                System.out.println(list.subList(i, i + 2));
                System.out.println(num);
            }

        }

    }

public static String formatList(List<String> list, String delimiter) {
        return String.join(delimiter, list);
    }
    
    
//输出：
1&2
3&4
5&6
```


> 参考：lombok @EqualsAndHashCode 注解的影响--<https://www.cnblogs.com/liaojie970/p/8939951.html>
> 参考：Java8（2）：Java8 对字符串连接的改进:<https://segmentfault.com/a/1190000007835105>


文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！
