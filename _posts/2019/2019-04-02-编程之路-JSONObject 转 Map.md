---
layout:     post             				# 使用的布局（不需要改）
title:         JSONObject 转 Map		 	 # 标题 
subtitle:   常见应用场景调用第三方接口 					  				#副标题
date:       2019-04-02  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
---

> 本文首次发布于[My Blog](http://uniquezhangqi.top),作者[@张琦(Ian)](http://uniquezhangqi.top/about/),转载请保留原文链接。

JSONObject 转 Map 并使用 Lambda 表达式；应用场景：调用第三方接口。

```java
        JSONObject jsonObject = new JSONObject();
        Object[] objects = {"1", "2", "3", "4"};
        Object[] objects1 = {"11", "22", "33", "44"};
        Object[] objects2 = {"111", "222", "333", "444"};
        Object[] objects3 = {"xxx123456"};
        jsonObject.put("A", objects);
        jsonObject.put("B", objects1);
        jsonObject.put("C", objects2);
        jsonObject.put("password", objects3);

        // JSONObject 转 Map
        Map<String, String> a = JSONObject.parseObject(jsonObject.toJSONString(), new TypeReference<Map<String, String>>() {
        });

        System.out.println("-----------：" + jsonObject);
        System.out.println("-----------：" + a);

        StringBuilder stringBuilder = new StringBuilder();
        a.forEach((key, values) -> {
            stringBuilder.append(key).append("=");
            if ("password".equalsIgnoreCase(key)) {
                stringBuilder.append("*****");
            } else {
                stringBuilder.append(values);
            }
            stringBuilder.append("&");
        });
        stringBuilder.deleteCharAt(stringBuilder.length() - 1);
        System.out.println("-----------：" + stringBuilder);

    }
```

```java
// 输出：
-----------：{"A":["1","2","3","4"],"B":["11","22","33","44"],"password":["xxx123456"],"C":["111","222","333","444"]}

-----------：{A=["1","2","3","4"], B=["11","22","33","44"], password=["xxx123456"], C=["111","222","333","444"]}

-----------：A=["1","2","3","4"]&B=["11","22","33","44"]&password=*****&C=["111","222","333","444"]
```


文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！

