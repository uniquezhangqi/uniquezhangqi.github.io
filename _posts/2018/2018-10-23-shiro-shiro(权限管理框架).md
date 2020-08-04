---
layout:     post             				# 使用的布局（不需要改）
title:         shiro各个组件及概念 	# 标题 
subtitle:    					  				#副标题
date:       2018-10-27  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - 编程之路
    - 鉴权
---
> 参考Apache Shiro官方API ：http://shiro.apache.org/architecture.html

## shiro
Apache Shiro是一个强大且易用的Java安全框架,执行身份验证、授权、密码学和会话管理。使用Shiro的易于理解的API,您可以快速、轻松地获得任何应用程序,从最小的移动应用程序到最大的网络和企业应用程序。

Shiro架构有三个主要的概念：Subject，SecurityManager和Realm 如下图关系：
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-ShiroBasicArchitecture.png)

#### Subject：
正如我们教程中提到的，Subject是程序安全视角的user。然而user通常指人类，但Subject可以是人，也可以指第三方的服务，守护进程账户，定时任务，或者其他类似的东西-基本上可以是和软件交互的任何事物。

Subject 的实例都绑定在SecurityManager上。当你和subject交互时，SecurityManager把交互转化为subject领域的交互。

#### SecurityManager：
SecurityManager是Shiro架构的核心，他扮演着伞对象的角色（持有各组件引用，协调调用），来协调它内部的安全组件，他们一块来形成对象视图。然而，一旦程序中的SecurityManager和他内部对象已经配置好，通常上就不用再管他，程序的开发人员几乎大部分时间都会花在Subject API上。

SeccurityManager。当你和Subject交互时，所做的任何Subject的安全操作，实际上是SecurityManager在幕后辛苦劳作。从上面的基础流程图中可以看到。

#### Realms：
Realms在Shiro和你的程序安全数据之间，扮演着桥梁或者连接器的角色。当和安全相关数据有交互时，比如用户登录校验和鉴权，Shiro通过程序中配置的一个或者多个Realm来查询。

在此场景中，Realm是安全领域的DAO：它封装了数据源连接的细节，使得相关数据对于shiro可用。当你配置Shiro，你必须指定至少一个realm用来登录和授权。SecurityManager可能会配置多个Realm，但是至少有一个。

Shrio提供了开箱即用的Realm来链接各种安全数据源，例如LDAP，关系型数据库（JDBC），文本配置如INI或者properties文件，等等。如果默认的Realm不能满足你的需求，你可以插入你自己的Realm实现来表述客制化的数据源。

就像其他内部的组件，Shiro的SecurityManager管理如何使用Realm来获取Subject实例相关的安全和身份信息。

### 下图表展示了Shiro的核心架构概念
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2018-10-27-ShiroArchitecture.png)

#### Subject
安全领域层面命名的，当前和系统交互的实体（用户，第三方服务，定时任务等等）

#### SecurityManager
如上文所述，SecurityManager是Shiro架构的核心。他很像是一个伞对象，来协调他所管理的组件，确保她们配合丝滑。它同时管理Shiro视角的每一个用户，他知道怎么执行每个用户的安全操作。

#### Authenticator
Authenticator组件负责执行用户的登录操作。当用户尝试登录时，Authenticator会执行登录逻辑。Authenticator知道怎么和存储用户账户信息的一个或多个Realm协调工作。Realm中维护的数据用来检查用户身份，确保这个用户就是他所说的那个用户。

#### Authentication Strategy
如果配置了多个Realm，AuthenticationStrategy将会协调Realm来确定条件来判断认证成功或者失败。（例如，如果一个realm成功了，但是其他的失败了，那么登录成功了吗？必须所有的realm都成功？还是仅是第一个？）

#### Authorizer
Authorizer组件负责确保用户的访问控制。说白了，他就是管控用户可以或者不可以做什么事情。就像Authenticator，Authorizer也知道如何和多种数据源协调来访问role和permission信息。Authorizer使用这些信息来验证用户是否允许做一个给定的操作。

#### SessionManager
SessionManager负责创建和管理用户的session生命周期，在任何环境中都可以给用户提供健壮的session体验。这在安全框架世界中是第一无二的特性----Shiro在任何环境，甚至没有Web/servlet或者EJB的环境中，都具备原生管理用户session的能力。默认情况下，Shiro会尽可能使用已经存在的session机制（比如Servlet Container），但是如果没有存在的session机制，例如在独立的程序或者非web环境，他会使用内置的企业session管理来提供同样的编程体验。既有的SessionDAO允许任使用何数据源持久化session

#### SessionDAO
SessionManager操作SessionDAO来执行session持久化（CRUD）操作。这可以允许任何数据存储被插入到session管理的基础设施中。

#### CacheManager
CacheManager创建和管理其它Shiro组件使用的缓存实例生命周期。因为Shiro认证、授权、session管理中可以访问多种后端数据源，缓存一直以来都是最优级别的架构特性，来提高使用这些数据的性能。任何的现代开源或者企业级缓存产品都可以插入到Shiro中来提供快速高效的用户体验。

#### Cryptography
Cryptography对于企业级安全框架是顺理成章的附加物。Shiro的crypto包含有易于使用和理解的密码工具，Hash（也称作摘要）和多种多样的编码实现。这个包中所有的类都精心设计以确保易于使用和理解。任何使用过java原生加密包的都知道它像驯兽一样困难。Shiro的API简化了Java机制，使得普通人也可以很简单的去使用。

#### Realms
如上文所述，Realms在Shiro和你应用的安全数据间扮演着桥梁或者连接器的角色。当发生一次真实的安全数据交互，比如用户登录和鉴权，Shiro会从程序配置好的一或多个Realm中查询很多次数据。你可以配置你所需要的任意数量的Realm，Shrio在授权和鉴权的时候会去协调使用他们。









