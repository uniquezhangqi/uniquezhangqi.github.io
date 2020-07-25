---
layout:     post             				# 使用的布局（不需要改）
title:        使用ConfigFilter在Spring Boot配置数据库密码加解密   # 标题 
subtitle:    					  				#副标题
date:       2018-11-04  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
---




# 使用ConfigFilter

ConfigFilter的作用包括：

- 从配置文件中读取配置
- 从远程http文件中读取配置
- 为数据库密码提供加密功能

## 1 配置ConfigFilter

### 1.1 配置文件从本地文件系统中读取
```
 <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
​     init-method="init" destroy-method="close">
​     <property name="filters" value="config" />
​     <property name="connectionProperties" value="config.file=file:///home/admin/druid-pool.properties" />
 </bean>
```

### 1.2 配置文件从远程http服务器中读取

```
 <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
​     init-method="init" destroy-method="close">
​     <property name="filters" value="config" />
​     <property name="connectionProperties" value="config.file=http://127.0.0.1/druid-pool.properties" />
 </bean>
```
这种配置方式，使得一个应用集群中，多个实例可以从同一个地方读取配置，集中配置，集中修改，部署更简单。

### 1.3 通过jvm启动参数来使用ConfigFilter
DruidDataSource支持jvm启动参数配置filters，所以你可以：
```
java -Ddruid.filters=config ....
```
## 2 数据库密码加密
数据库密码直接写在配置中，对运维安全来说，是一个很大的挑战。Druid为此提供一种数据库密码加密的手段ConfigFilter。

### 2.1 执行命令加密数据库密码
在命令行中执行如下命令：

```
java -cp druid-1.0.16.jar com.alibaba.druid.filter.config.ConfigTools you_password
```
输出
```
privateKey:MIIBVgIBADANBgkqhkiG9w0BAQEFAASCAUAwggE8AgEAAkEA6+4avFnQKP+O7bu5YnxWoOZjv3no4aFV558HTPDoXs6EGD0HP7RzzhGPOKmpLQ1BbA5viSht+aDdaxXp6SvtMQIDAQABAkAeQt4fBo4SlCTrDUcMANLDtIlax/I87oqsONOg5M2JS0jNSbZuAXDv7/YEGEtMKuIESBZh7pvVG8FV531/fyOZAiEA+POkE+QwVbUfGyeugR6IGvnt4yeOwkC3bUoATScsN98CIQDynBXC8YngDNwZ62QPX+ONpqCel6g8NO9VKC+ETaS87wIhAKRouxZL38PqfqV/WlZ5ZGd0YS9gA360IK8zbOmHEkO/AiEAsES3iuvzQNYXFL3x9Tm2GzT1fkSx9wx+12BbJcVD7AECIQCD3Tv9S+AgRhQoNcuaSDNluVrL/B/wOmJRLqaOVJLQGg==
publicKey:MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAOvuGrxZ0Cj/ju27uWJ8VqDmY7956OGhVeefB0zw6F7OhBg9Bz+0c84RjzipqS0NQWwOb4kobfmg3WsV6ekr7TECAwEAAQ==
password:PNak4Yui0+2Ft6JSoKBsgNPl+A033rdLhFw+L0np1o+HDRrCo9VkCuiiXviEMYwUgpHZUFxb2FpE0YmSguuRww==
```
输入你的数据库密码，输出的是加密后的结果。

### 2.2 配置数据源，提示Druid数据源需要对数据库密码进行解密。
```
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
​     init-method="init" destroy-method="close">
​     <property name="url" value="jdbc:derby:memory:spring-test;create=true" />
​     <property name="username" value="sa" />
​     <property name="password" value="${password}" />
​     <property name="filters" value="config" />
​     <property name="connectionProperties" value="config.decrypt=true;config.decrypt.key=${publickey}" />
</bean>
```
### 2.3 配置参数，让ConfigFilter解密密码
有三种方式配置：

- 可以在配置文件my.properties中指定config.decrypt=true 
- 也可以在DruidDataSource的ConnectionProperties中指定config.decrypt=true 
- 也可以在jvm启动参数中指定-Ddruid.config.decrypt=true 



文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！


