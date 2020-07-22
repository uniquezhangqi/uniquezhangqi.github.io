---
layout:     post             				# 使用的布局（不需要改）
title:         springboot动态数据源动态切换  	# 标题 
subtitle:    					  				#副标题
date:       2019-06-04 					# 时间
author:     Ian                  			# 作者
header-img: img/ian-bg-red.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
---

## 前言
![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-06-04-Screenshot%202019-06-04%20at%2016.27.31.png)

详细原理请参考：[原基于Spring Boot实现Mybatis的多数据源切换和动态数据源加载](https://blog.csdn.net/YHYR_YCY/article/details/78894940)

## 核心
由于公司项目需求每一个新入驻商户需要一个独立的库，这就需要动态创建库，动态切换。

经过一番查阅资料，使用核心东西就是通过 `spring AbstractRoutingDataSource` 为我们抽象了一个 `DynamicDataSource` 来解决这一问题的

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-06-04-Screenshot%202019-06-04%20at%2016.11.30.png)

### 简单分析下 AbstractRoutingDataSource 的源码

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-06-04-Screenshot%202019-06-04%20at%2016.14.52.png)

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-06-04-Screenshot%202019-06-04%20at%2016.17.28.png)

`targetDataSources` 就是我们的多个数据源，在初始化的时候会调用`afterPropertiesSet()`，去解析我们的数据源 然后 `put` 到 `resolvedDataSources`

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-06-04-Screenshot%202019-06-04%20at%2016.19.50.png)

实现了 `DataSource` 的 `getConnection()`; 我们看看 `determineTargetDataSource()`; 做了什么

![](http://uniquezhangqi.oss-cn-shenzhen.aliyuncs.com/blog/2019-06-04-Screenshot%202019-06-04%20at%2016.21.23.png)

通过下面的 `determineCurrentLookupKey()`;（这个方法需要我们实现） 返回一个key，然后从` resolvedDataSources` （其实也就是 `targetDataSources`） 中 get 一个数据源，实现了每次调用` getConnection()`; 打开连接 切换数据源，如果想动态添加的话 只需要重新` set targetDataSources` 再调用` afterPropertiesSet()`。

## 以下是 demo 代码截图：

```java

import com.alibaba.druid.pool.DruidDataSource;
import org.apache.ibatis.session.SqlSessionFactory;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.SqlSessionTemplate;
import org.mybatis.spring.boot.autoconfigure.MybatisProperties;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import org.springframework.jdbc.datasource.DataSourceTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;

import javax.sql.DataSource;
import java.util.HashMap;
import java.util.Map;

/**
 * @className: DataSourceConfigurer
 * @description: 数据源配置类, 在tomcat启动时触发，在该类中生成多个数据源实例并将其注入到 ApplicationContext 中
 * @author: Ian
 * @create: 2019-05-23 14:25
 **/
@Configuration
@EnableConfigurationProperties(MybatisProperties.class)
public class DataSourceConfigurer {
    /**
     * 日志logger句柄
     */
    private final Logger logger = LoggerFactory.getLogger(getClass());

    /**
     * 自动注入环境类，用于获取配置文件的属性值
     */
    @Autowired
    private Environment evn;

    private MybatisProperties mybatisProperties;

    public DataSourceConfigurer(MybatisProperties properties) {
        this.mybatisProperties = properties;
    }


    /**
     * 创建数据源对象   todo 读取配置文件
     *
     * @param dbType 数据库类型
     * @return data source
     */
    private DruidDataSource createDataSource(String dbType) {
        //如果不指定数据库类型，则使用默认数据库连接
        String dbName = dbType.trim().isEmpty() ? "default" : dbType.trim();
        DruidDataSource dataSource = new DruidDataSource();
        String prefix = "db." + dbName + ".";
        String dbUrl = evn.getProperty(prefix + "url-base")
                + evn.getProperty(prefix + "host") + ":"
                + evn.getProperty(prefix + "port") + "/"
                + evn.getProperty(prefix + "dbname") + evn.getProperty(prefix + "url-other");
        logger.info("+++default默认数据库连接url = " + dbUrl);
        dataSource.setUrl(dbUrl);
        dataSource.setUsername(evn.getProperty(prefix + "username"));
        dataSource.setPassword(evn.getProperty(prefix + "password"));
        dataSource.setDriverClassName(evn.getProperty(prefix + "driver-class-name"));
        return dataSource;
    }

    /**
     * spring boot 启动后将自定义创建好的数据源对象放到TargetDataSources中用于后续的切换数据源用
     * (比如：DynamicDataSourceContextHolder.setDataSourceKey("dbMall")，手动切换到dbMall数据源
     * 同时指定默认数据源连接
     *
     * @return 动态数据源对象
     */
    @Bean
    public DynamicDataSource dynamicDataSource() {
        //获取动态数据库的实例（单例方式）
        DynamicDataSource dynamicDataSource = DynamicDataSource.getInstance();
        //创建默认数据库连接对象
        DruidDataSource defaultDataSource = createDataSource("default");
        //创建db_mall数据库连接对象
//        DruidDataSource mallDataSource = createDataSource("dbMall");

        Map<Object, Object> map = new HashMap<>();
        //自定义数据源key值，将创建好的数据源对象，赋值到targetDataSources中,用于切换数据源时指定对应key即可切换
        map.put("default", defaultDataSource);
//        map.put("dbMall", mallDataSource);
        dynamicDataSource.setTargetDataSources(map);
        //设置默认数据源
        dynamicDataSource.setDefaultTargetDataSource(defaultDataSource);

        return dynamicDataSource;
    }

    /**
     * 　配置mybatis的sqlSession连接动态数据源
     *
     * @param dynamicDataSource
     * @return
     * @throws Exception
     */
    @Bean
    public SqlSessionFactory sqlSessionFactory(
            @Qualifier("dynamicDataSource") DataSource dynamicDataSource)
            throws Exception {
        SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
        bean.setDataSource(dynamicDataSource);
        bean.setMapperLocations(mybatisProperties.resolveMapperLocations());
        bean.setTypeAliasesPackage(mybatisProperties.getTypeAliasesPackage());
        bean.setConfiguration(mybatisProperties.getConfiguration());
        return bean.getObject();
    }

    @Bean(name = "sqlSessionTemplate")
    public SqlSessionTemplate sqlSessionTemplate(
            @Qualifier("sqlSessionFactory") SqlSessionFactory sqlSessionFactory)
            throws Exception {
        return new SqlSessionTemplate(sqlSessionFactory);
    }
```

```java

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.jdbc.datasource.lookup.AbstractRoutingDataSource;
import org.springframework.stereotype.Component;

import java.util.HashMap;
import java.util.Map;

/**
 * @className: DynamicDataSource
 * @description: 动态数据设置以及获取，本类属于单例
 * @author: Ian
 * @create: 2019-05-23 14:24
 **/
@Component
public class DynamicDataSource extends AbstractRoutingDataSource {

    private final Logger logger = LoggerFactory.getLogger(getClass());
    /**
     * 单例句柄
     */
    private static DynamicDataSource instance;
    private static byte[] lock = new byte[0];
    /**
     * 用于存储已实例的数据源map
     */
    private static Map<Object, Object> dataSourceMap = new HashMap<>();

    /**
     * 获取当前数据源
     *
     * @return
     */
    @Override
    protected Object determineCurrentLookupKey() {
        logger.info("Current DataSource is [{}]", DynamicDataSourceContextHolder.getDataSourceKey());
        return DynamicDataSourceContextHolder.getDataSourceKey();
    }

    /**
     * 设置数据源
     *
     * @param targetDataSources
     */
    @Override
    public void setTargetDataSources(Map<Object, Object> targetDataSources) {
        super.setTargetDataSources(targetDataSources);
        dataSourceMap.putAll(targetDataSources);
        super.afterPropertiesSet();// 必须添加该句，否则新添加数据源无法识别到
    }

    /**
     * 获取存储已实例的数据源map
     *
     * @return
     */
    public Map<Object, Object> getDataSourceMap() {
        return dataSourceMap;
    }

    /**
     * 单例方法
     *
     * @return
     */
    public static synchronized DynamicDataSource getInstance() {
        if (instance == null) {
            synchronized (lock) {
                if (instance == null) {
                    instance = new DynamicDataSource();
                }
            }
        }
        return instance;
    }

    /**
     * 是否存在当前key的 DataSource
     *
     * @param key
     * @return 存在返回 true, 不存在返回 false
     */
    public static boolean isExistDataSource(String key) {
        return dataSourceMap.containsKey(key);
    }
}
```

```java
package top.uniquezhangqi.dynamic;

/**
 * @className: DynamicDataSourceContextHolder
 * @description: 通过 ThreadLocal 获取和设置线程安全的数据源 key
 * @author: Ian
 * @create: 2019-05-23 14:27
 **/
public class DynamicDataSourceContextHolder {

    /**
     * Maintain variable for every thread, to avoid effect other thread
     */
    private static final ThreadLocal<String> contextHolder = new ThreadLocal<String>() {
        /**
         * 将 default 数据源的 key 作为默认数据源的 key
         */
//        @Override
//        protected String initialValue() {
//            return "default";
//        }
    };


    /**
     * To switch DataSource
     *
     * @param key the key
     */
    public static synchronized void setDataSourceKey(String key) {
        contextHolder.set(key);
    }

    /**
     * Get current DataSource
     *
     * @return data source key
     */
    public static String getDataSourceKey() {
        return contextHolder.get();
    }

    /**
     * To set DataSource as default
     */
    public static void clearDataSourceKey() {
        contextHolder.remove();
    }
}
```

```java

import com.alibaba.druid.pool.DruidDataSource;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.env.Environment;
import org.springframework.stereotype.Component;
import org.springframework.transaction.PlatformTransactionManager;

import java.util.HashMap;
import java.util.Map;

/**
 * @className: SwitchDB
 * @description: 切换数据库类
 * @author: Ian
 * @create: 2019-05-23 14:28
 **/
@Configuration
@Slf4j
@Component
public class SwitchDB {
    @Autowired
    private Environment evn;
    //私有库数据源key
    private static String ljyunDataSourceKey = "ljyun_";

    @Autowired
    DynamicDataSource dynamicDataSource;

    @Autowired
    private PlatformTransactionManager transactionManager;

    /**
     * 切换数据库对外方法,如果私有库id参数非0,则首先连接私有库，否则连接其他已存在的数据源
     *
     * @param dbName  已存在的数据库源对象
     * @param ljyunId 私有库主键
     * @return 返回当前数据库连接对象对应的key
     */
    public String change(String dbName, int ljyunId) throws Exception {
        if (ljyunId == 0) {
            toDB(dbName);
        } else {
            toYunDB(ljyunId);
        }
        //获取当前连接的数据源对象的key
        String currentKey = DynamicDataSourceContextHolder.getDataSourceKey();
        log.info("＝＝＝＝＝当前连接的数据库是:" + currentKey);
        return currentKey;
    }

    /**
     * 切换已存在的数据源
     *
     * @param dbName
     */
    private void toDB(String dbName) {
        //如果不指定数据库，则直接连接默认数据库
        String dbSourceKey = dbName.trim().isEmpty() ? "default" : dbName.trim();
        //获取当前连接的数据源对象的key
        String currentKey = DynamicDataSourceContextHolder.getDataSourceKey();
        //如果当前数据库连接已经是想要的连接，则直接返回
        if (currentKey == dbSourceKey) return;
        //判断储存动态数据源实例的map中key值是否存在
        if (DynamicDataSource.isExistDataSource(dbSourceKey)) {
            DynamicDataSourceContextHolder.setDataSourceKey(dbSourceKey);
            log.info("＝＝＝＝＝普通库: " + dbName + ",切换完毕");
        } else {
            log.info("切换普通数据库时，数据源key=" + dbName + "不存在");
        }
    }

    /**
     * 创建新的私有库数据源
     *
     * @param ljyunId
     */
    private void toYunDB(int ljyunId) {
        //组合私有库数据源对象key
        String dbSourceKey = ljyunDataSourceKey + ljyunId;
        //获取当前连接的数据源对象的key
        String currentKey = DynamicDataSourceContextHolder.getDataSourceKey();
        if (dbSourceKey == currentKey) {
            return;
        }

        //创建私有库数据源
        createLjyunDataSource(ljyunId);

        //切换到当前数据源
        DynamicDataSourceContextHolder.setDataSourceKey(dbSourceKey);
        log.info("＝＝＝＝＝私有库: " + ljyunId + ",切换完毕");
    }

    /**
     * 创建私有库数据源，并将数据源赋值到targetDataSources中，供后切库用
     *
     * @param ljyunId
     * @return
     */
    private DruidDataSource createLjyunDataSource(int ljyunId) {
        //创建新的数据源
        if (ljyunId == 0) {
            log.info("动态创建私有库数据时，私有库主键丢失");
        }
        String yunId = String.valueOf(ljyunId);
        DruidDataSource dataSource = new DruidDataSource();
        String prefix = "db.privateDB.";
        String dbUrl = evn.getProperty(prefix + "url-base")
                + evn.getProperty(prefix + "host") + ":"
                + evn.getProperty(prefix + "port") + "/"
                + evn.getProperty(prefix + "dbname").replace("{id}", yunId) + evn.getProperty(prefix + "url-other");
        log.info("+++创建云平台私有库连接url = " + dbUrl);
        dataSource.setUrl(dbUrl);
        dataSource.setUsername(evn.getProperty(prefix + "username"));
        dataSource.setPassword(evn.getProperty(prefix + "password"));
        dataSource.setDriverClassName(evn.getProperty(prefix + "driver-class-name"));

        //将创建的数据源，新增到targetDataSources中
        Map<Object, Object> map = new HashMap<>();
        map.put(ljyunDataSourceKey + yunId, dataSource);
//        DynamicDataSource.getInstance().setTargetDataSources(map);
        return dataSource;
    }
}
```

```java
package top.uniquezhangqi.dynamic;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * @className: TestTransaction
 * @description: 测试切库后的事务类
 * @author: Ian
 * @create: 2019-05-23 14:29
 **/
@Service
@Slf4j
public class TestTransaction {

    @Autowired
    private SwitchDB switchDB;

    @Autowired
    DynamicDataSource dynamicDataSource;

    @Autowired
    TestTransactionProcess testTransactionProcess;

    public String testProcess(int ljyunId, String dbName) {
        log.info("***********************************:" + switchDB);
        try {
            switchDB.change(dbName, ljyunId);
        } catch (Exception e) {
            log.error("---------------------------------------------:", e.getMessage());
        }
        //获取当前已有的数据源实例
        System.out.println("-----------------------------------:" + dynamicDataSource.getDataSourceMap());

        return testTransactionProcess.process(ljyunId, dbName);
    }
}
```

```java
package top.uniquezhangqi.dynamic;

import top.uniquezhangqi.domain.Product;
import top.uniquezhangqi.mapper.ProductDao;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Propagation;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.transaction.interceptor.TransactionAspectSupport;

/**
 * @className: TestTransactionProcess
 * @description: 事务测试
 * @author: Ian
 * @create: 2019-05-24 14:42
 **/
@Slf4j
@Repository
public class TestTransactionProcess {


    @Autowired
    private ProductDao dao;

    /**
     * 事务测试
     * 注意：(1)有@Transactional注解的方法，方法内部不可以做切换数据库操作
     * (2)在同一个service其他方法调用带@Transactional的方法，事务不起作用，（比如：在本类中使用testProcess调用process()）
     * 可以用其他service中调用带@Transactional注解的方法，或在controller中调用.
     *
     * @param ljyunId
     * @param dbName
     * @return
     */
    //propagation 传播行为 isolation 隔离级别  rollbackFor 回滚规则
    @Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.DEFAULT, timeout = 36000, rollbackFor = Exception.class)
    public String process(int ljyunId, String dbName) {
        String currentKey = DynamicDataSourceContextHolder.getDataSourceKey();
        log.info("＝＝＝＝＝service当前连接的数据库是:" + currentKey);
        Product exhibition = new Product();
        exhibition.setId(767676767);
        exhibition.setName("7676767676767");
        exhibition.setPrice(2432423);
        //return new DomainResponse<String>(1, "新增成功", "");
        int addRes = dao.insert(exhibition);

//        if(addRes>0 && kaiguan==1){
        if (0 == 1) {
            exhibition.setName("3333333333");
            int addRes2 = dao.insert(exhibition);
            return "新增成功";
        } else {
//            Map<String,String> map = new HashMap<>();
//            String a = map.get("hello");
            //log.info("-----a="+a.replace("a","b"));
            //手动回滚事务
            TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
            return "新增错误，事务已回滚";
        }
    }
}
```

本文完整源码GitHub：[spring-dynamic-datasource](https://github.com/uniquezhangqi/spring-dynamic-datasource)

> - **参考：**
> - [原基于Spring Boot实现Mybatis的多数据源切换和动态数据源加载](https://blog.csdn.net/YHYR_YCY/article/details/78894940)
> - [spring 动态切换数据源 多数据库](https://blog.csdn.net/laojiaqi/article/details/78964862)
> - [SpringBoot+Mybatis-多数据源动态切换＋动态加载](https://www.jianshu.com/p/7f1b785cd986)

文章中若有不足之处，还望指出。坚持是一种精神，分享是一种快乐！