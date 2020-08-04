---
layout:     post             				# 使用的布局（不需要改）
title:      IDEA:Error during artifact deployment. See server log for details.详解          			# 标题 
subtitle:    					  				#副标题
date:       2018-03-24  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-re-vs-ng2.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - 编程之路
---



## Error during artifact deployment. See server log for details详解.

#### 可能出错的地方：

1. web.xml文件   web应用部署描述符，里面的部署的xml文件或者类，如果这些找不到就会发生startup failed due to previous errors错误。

2. 如果在应用spring的话，在配置文件applicationContext.xml中定义的类、xml文件找不到也会报这个错误。

3. 在web.xml，struts.xml，applicationContext.xml文件中自身有任何一点错误都可能引起上面的这个问题，而不仅仅是附带的文件错误导致。

4. 如果使用ibatis的话，在SqlMapConfig.xml中定义的xml文件找不到也会报这个错误。（hibernate的配置在整合spring的时候使用spring的配置文件）

5. JDK的版本问题，最好使用JDK5.0 或者更高的版本。

6. Eclipse和tomcat的版本兼容问题

7. 框架整合的过程中在导入到lib下的jar包冲突也可能产生该错误。

8. jar包的缺少以及jar包的版本也可产生该错误。

9. 其他的原因

#### 我的问题：

1. `<listener>`的生命周期没走完。-- web.xml里面`<listener>`是从上往下顺序执行。
2. 在InitLoadJobRunListener还用Spring的注解注入了。

然后Tomcat就报标题错误，最后根据下面解决方案解决。   

#### 解决方案：
　　我用的是`Spring、SpringMVC、SpringDataJpa、Maven`Tomcat 启动不起来，报标题错误。下面截图是`web.xml`里面的片段：
　　![](https://ws4.sinaimg.cn/large/006tKfTcgy1fpphge1nrij30l005jt8k.jpg)

1. 第一个`<listener-class>`是载入spring的上下文，加载spring的配置文件。　　
2. 第二个`<listener-class>`是获取spring启动完毕以后为工具类注入 spring上下文,方便获取spring上下文直接得到bean实例，会实现spring的ServletContextListener接口。
3. 第三个`<listener-class>`是我写的定时任务--启动系统时需要启动状态为开启的job一次需要用到的--InitLoadJobRunListener类里面spring的注解还有log4j都是不起作用的，还有几个也是不归spring管理的，也不会起作用。

#### 希望对你们有所帮助！！！
最后感谢**连晋**大佬对我的帮助。



