---
layout:     post             				# 使用的布局（不需要改）
title:      SpringDataJPA几种sql方式     # 标题 
subtitle:   👣 					  			#副标题
date:       2018-04-07  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-universe.jpg 	#这篇文章标题背景图片
catalog: true                        	# 是否归档
iscopyright: true                      # 是否版权
tags:                              		#标签
    - 编程之路
    - 自我总结
    - DB
---




```java

/**
 * SpringDataJPA 几种sql方式。
 */

public boolean login(String name,String password){

 //   User user = userDao.findByNameAndPassword(name,password);

 /**   User user = userDao.findOne(new Specifiaction<User>() {
        public Predicate toPredicate(Root<User> root, CrieriaQuery<?> cq, CriteriaBuilder cb){
            return cb.and(
                cb.equal(
                    root.get("name").as(String.class),
                    name
                ),
                cb.equal(
                    root.get("password").as(Stirng.class),
                    password
                )
            );
        }

    });
    return user != null;
*/

@Modifying
@Query("delete User u where u.id =: id")
@Transactional
void delByUserId(@Param("id") Long id);
// select * from user where name =? and password =?


/**
* JPA 运行原生 sql 语句进行删除 一个user
*/
@Modifying
@Query(value="DELETE FORM user WHERE id=:id",nativeQuery = true)
@Transactional
void delUserByNativeSql(@Param("id")Long id);

}
```


1. @Component	加到类路径自动扫描
2. @Controller	一个web的控制层，在Spring MVC中使用
3. @Repository	数据管理/存储,企业级应用使用(Dao, DDD)
4. @Service	提供一个商业逻辑 - 一个无状态的切面

`Page<AcquisitionResults> AcquisitionResultsPage = acquisitionResultsService.findAll((root, cq, cb) -> {`

> root 路径相关  <https://www.objectdb.com/api/java/jpa/criteria/Root>

> cq 查询相关   <https://docs.oracle.com/javaee/6/api/javax/persistence/criteria/CriteriaQuery.html>

> cb 创建相关   <https://docs.oracle.com/javaee/7/api/javax/persistence/criteria/CriteriaBuilder.html>
条件对象<br>
equal  等价于  and<br>
大于  gn<br>
小于  le<br>

#### springDataJpa 需要注意的注解
1. @Modifying
2. @Query(value="DELETE from user where id=:id",nativeQuery = true  这个里面的sql语句 前面是 类名 不是表名)  加nativeQuery = true 表示使用原生sql 
3. @Transactional


#### shiro生命周期
生命周期：

- spring 把shiro 注入进去
- 例子：
    * 登录请求会转发给shiro 里面
    * controller servlet 让shiro 来管理servlet
    * 用aop的方式去管理所有的controller，然后定义用shiro来代理它

- 白名单：
    * `spring-shiro.xml:` anon 当前所有请求登录都可以处理

    * logout 登出

    * /** = authc 指定哪个请求   验证器

    * /** = user 除了上面的，其它的都要登录以后才能访问。 

- redirect：/admin/index   redirect转发。



