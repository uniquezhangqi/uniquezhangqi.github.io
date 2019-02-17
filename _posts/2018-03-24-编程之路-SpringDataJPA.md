---
layout:     post             				# 使用的布局（不需要改）
title:      SpringDataJPA          			# 标题 
subtitle:   	  				#副标题
date:       2018-03-25  					# 时间
author:     Ian                  			# 作者
header-img: img/post-bg-re-vs-ng2.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - SpringDataJPA
---

## Spring Data JPA
　　自从用了Spring Data JPA之后个人感觉比hibernate、mybatis好用太多了---SpringData JAP非常契合OOP思想。如：设计模式的[开放-封闭原则、依赖倒转原则、单一职责](http://uniquezhangqi.top/2018/02/04/自我总结-设计模式总结(一)/)、[迪米特法则](http://uniquezhangqi.top/2018/03/02/自我总结-设计模式总结(二)/)等等，也是OOP非常非常核心的东西（按住Ctrl+鼠标左键点击蓝色的可以查看对应模式)...我的GitHub上最近在更新一个[Spring、SpringDataJPA、shiro](https://github.com/uniquezhangqi/SpringDemo)框架整合的一个小项目,有兴趣可以瞅瞅；下面是我转载的一篇SpringDataJPA入门的文章，GitHub上还有一个[Demo](https://github.com/spring-projects/spring-data-examples/tree/master/jpa/showcase)。
　　
#### The domain

为了简单起见，我们从一个很小的着名域开始：我们`Customer`和`Accounts`。

```java
@Entity
public class Customer {

  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  private Long id;

  private String firstname;
  private String lastname;

  // … methods omitted
}
```

```java
@Entity
public class Account {

  @Id
  @GeneratedValue(strategy = GenerationType.AUTO)
  private Long id;

  @ManyToOne
  private Customer customer;

  @Temporal(TemporalType.DATE)
  private Date expiryDate;

  // … methods omitted
}
```

该`Account`有我们将在稍后阶段使用的有效期限。除此之外，对于类或映射没有什么特别的地方 - 它使用普通的JPA注释。现在我们来看看组件管理`Account`对象

```java
@Repository
@Transactional(readOnly = true)
class AccountServiceImpl implements AccountService {

  @PersistenceContext
  private EntityManager em;

  @Override
  @Transactional
  public Account save(Account account) {

    if (account.getId() == null) {
      em.persist(account);
      return account;
    } else {
      return em.merge(account);
    }
  }

  @Override
  public List<Account> findByCustomer(Customer customer) {

    TypedQuery query = em.createQuery("select a from Account a where a.customer = ?1", Account.class);
    query.setParameter(1, customer);

    return query.getResultList();
  }
}
```

我故意命名该类`*Service`以避免名称冲突，因为我们将在开始重构时引入一个存储库层。但从概念上讲，这里的课程是一个存储库而不是服务。那么我们在这里实际上有什么？

该类用注释`@Repository`来启用从JPA异常到Spring `DataAccessException`层次结构的异常转换。除此之外，我们使用它`@Transactional`来确保`save(…)`操作在事务中运行并允许设置`readOnly-flag（在类级别）findByCustomer(…)`。这会在持久性提供程序内部以及数据库级别上导致一些性能优化。

当我们想从决定是否拨打免费客户端`merge(…)`或`persist(…)`在EntityManager我们使用id的-场`Account`来决定我们是否考虑一个`Account`对象作为新的或不。这个逻辑当然可以被提取到一个通用的超类中，因为我们可能不希望为每个域对象特定的存储库实现重复此代码。查询方法也很简单：我们创建一个查询，绑定一个参数并执行查询以获得结果。这几乎让直行向前，人们可以把代码的执行作为样板与想象的有点是源自方法签名：我们预期List的`Accounts`，查询非常接近方法名称，我们只需将方法参数绑定到它。正如你所看到的，还有改进的空间。

#### Spring Data repository support

在我们开始重构实现之前，请注意，示例项目包含可以在重构过程中运行的测试用例，以验证代码是否仍然有效。现在让我们看看我们如何改进实施。

Spring Data JPA提供了一个以每个托管域对象的接口开始的存储库编程模型：

```java
public interface AccountRepository extends JpaRepository<Account, Long> { … }
```

定义这个接口有两个目的：首先，通过扩展，`JpaRepository`我们得到了一些通用的CRUD方法到我们的类型中，允许保存`Account`，删除它们等等。其次，这将允许Spring Data JPA存储库基础结构扫描该接口的类路径并为其创建一个`Spring bean`。

为了让Spring创建一个实现此接口的bean，您只需使用Spring JPA命名空间并使用相应的元素激活存储库支持：

```java
<jpa:repositories base-package="com.acme.repositories" />
```

这将扫描下面所有包的`com.acme.repositories`扩展接口，JpaRepository并为它创建一个`Spring bean`，它由一个实现支持`SimpleJpaRepository`。让我们迈出第一步，重新构建`AccountService`一下我们的实现来使用我们新引入的存储库接口：

```java
@Repository
@Transactional(readOnly = true)
class AccountServiceImpl implements AccountService {

  @PersistenceContext
  private EntityManager em;

  @Autowired
  private AccountRepository repository;

  @Override
  @Transactional
  public Account save(Account account) {
    return repository.save(account);
  }

  @Override
  public List<Account> findByCustomer(Customer customer) {

    TypedQuery query = em.createQuery("select a from Account a where a.customer = ?1", Account.class);
    query.setParameter(1, customer);

    return query.getResultList();
  }
}
```

在重构之后，我们只需将调用委托`save(…)`给存储库。默认情况下，如果存储库实现的`id`属性`null`与您在前面的示例中看到的一样（注意，如果需要，您可以获得对该决定的更详细控制），则存储库实现将考虑一个新的实体。此外，我们可以删除`@Transactional`该方法的注释，因为Spring Data JPA存储库实现的CRUD方法已经注释了`@Transactional`。

接下来我们将重构查询方法。让我们按照与save方法相同的查询方法的委托策略。我们在存储库接口上引入一个查询方法，并将我们原来的方法委托给新引入的方法：

```java
@Transactional(readOnly = true) 
public interface AccountRepository extends JpaRepository<Account, Long> {

  List<Account> findByCustomer(Customer customer); 
}
```

```java
@Repository
@Transactional(readOnly = true)
class AccountServiceImpl implements AccountService {

  @Autowired
  private AccountRepository repository;

  @Override
  @Transactional
  public Account save(Account account) {
    return repository.save(account);
  }

  @Override
  public List<Account> findByCustomer(Customer customer) {
    return repository.findByCustomer(Customer customer);
  }
}
```

让我在这里添加关于事务处理的简要说明。在这种非常简单的情况下，我们可以完全删除类中的`@Transactional`注释，`AccountServiceImpl`因为存储库的CRUD方法是事务性的，查询方法用`@Transactional(readOnly = true)`已经在储存库界面。当前的设置（服务级别的方法标记为事务性的（即使在这种情况下不需要））是最好的，因为在查看事务中正在发生的操作的服务级别时，它是明确清晰的。除此之外，如果修改服务层方法以对存储库方法进行多次调用，则所有代码仍将在单个事务中执行，因为存储库的内部事务只会加入在服务层启动的外部事务。存储库的交易行为和调整它的可能性在参考文档中详细记录。

尝试再次运行测试用例并查看它的工作原理。停止，我们没有提供任何`findByCustomer(…)`正确的实施？这个怎么用？

#### Query methods
当Spring Data JPA为`AccountRepository`接口创建Spring bean实例时，它将检查其中定义的所有查询方法并为它们中的每个查询派生查询。默认情况下，Spring Data JPA将自动分析方法名称并从中创建一个查询。该查询是使用JPA标准API实现的。在这种情况下，该`findByCustomer(…)`方法在逻辑上等同于JPQL查询`select a from Account a where a.customer = ?1`。该分析方法名解析器支持相当大集如关键字`And`，`Or`，`GreaterThan`，`LessThan`，`Like`，`IsNull`，`Not`等。`OrderBy`如果你喜欢，你也可以添加子句。有关详细概述，请查阅参考文档。这种机制为我们提供了一种查询方法编程模型，就像您在Grails或Spring Roo中使用的那样。

现在让我们假设你想明确要使用的查询。为此，您可以`Account.findByCustomer`在实体或您的注释中声明遵循命名约定（本例中为）的JPA命名查询`orm.xml`。或者，您可以使用以下注释库方法`@Query`：

```java
@Transactional(readOnly = true)
public interface AccountRepository extends JpaRepository<Account, Long> {

  @Query("<JPQ statement here>")
  List<Account> findByCustomer(Customer customer); 
}
```

现在让我们在`CustomerServiceImpl`应用我们迄今为止看到的功能之前/之后做一个比较：

```java
@Repository
@Transactional(readOnly = true)
public class CustomerServiceImpl implements CustomerService {

  @PersistenceContext
  private EntityManager em;

  @Override
  public Customer findById(Long id) {
    return em.find(Customer.class, id);
  }

  @Override
  public List<Customer> findAll() {
    return em.createQuery("select c from Customer c", Customer.class).getResultList();
  }

  @Override
  public List<Customer> findAll(int page, int pageSize) {

    TypedQuery query = em.createQuery("select c from Customer c", Customer.class);

    query.setFirstResult(page * pageSize);
    query.setMaxResults(pageSize);

    return query.getResultList();
  }

  @Override
  @Transactional
  public Customer save(Customer customer) {

    // Is new?
    if (customer.getId() == null) {
      em.persist(customer);
      return customer;
    } else {
      return em.merge(customer);
    }
  }

  @Override
  public List<Customer> findByLastname(String lastname, int page, int pageSize) {

    TypedQuery query = em.createQuery("select c from Customer c where c.lastname = ?1", Customer.class);

    query.setParameter(1, lastname);
    query.setFirstResult(page * pageSize);
    query.setMaxResults(pageSize);

    return query.getResultList();
  }
}
```

好的，我们先创建`CustomerRepository`并删除CRUD方法：

```java
@Transactional(readOnly = true)
public interface CustomerRepository extends JpaRepository<Customer, Long> { … }
```

```java
@Repository
@Transactional(readOnly = true)
public class CustomerServiceImpl implements CustomerService {

  @PersistenceContext
  private EntityManager em;

  @Autowired
  private CustomerRepository repository;

  @Override
  public Customer findById(Long id) {
    return repository.findById(id);
  }

  @Override
  public List<Customer> findAll() {
    return repository.findAll();
  }

  @Override
  public List<Customer> findAll(int page, int pageSize) {

    TypedQuery query = em.createQuery("select c from Customer c", Customer.class);

    query.setFirstResult(page * pageSize);
    query.setMaxResults(pageSize);

    return query.getResultList();
  }

  @Override
  @Transactional
  public Customer save(Customer customer) {
    return repository.save(customer);
  }

  @Override
  public List<Customer> findByLastname(String lastname, int page, int pageSize) {

    TypedQuery query = em.createQuery("select c from Customer c where c.lastname = ?1", Customer.class);

    query.setParameter(1, lastname);
    query.setFirstResult(page * pageSize);
    query.setMaxResults(pageSize);

    return query.getResultList();
  }
}
```

到现在为止还挺好。现在剩下的是处理常见场景的两种方法：您不想访问给定查询的所有实体，而仅访问其中的一个页面（例如，页面1的页面大小为10）。现在，这是用两个适当限制查询的整数来解决的。这有两个问题。两个整数实际上代表了一个概念，这里没有明确说明。除此之外，我们返回一个简单的`List`数据，因此我们失去了关于实际数据页面的元数据信息：它是第一页吗？这是最后一个吗？总共有几页？Spring Data提供了一个由两个接口组成的抽象`Pageable`（捕获分页请求信息）以及`Page`（捕获结果以及元信息）。所以让我们尝试添加`findByLastname(…)`到版本库接口并重写`findAll(…)`，`findByLastname(…)`如下所示：

```java
@Transactional(readOnly = true) 
public interface CustomerRepository extends JpaRepository<Customer, Long> {

  Page<Customer> findByLastname(String lastname, Pageable pageable); 
}
```

```java
@Override 
public Page<Customer> findAll(Pageable pageable) {
  return repository.findAll(pageable);
}

@Override
public Page<Customer> findByLastname(String lastname, Pageable pageable) {
  return repository.findByLastname(lastname, pageable); 
}
```

确保你根据签名的变化调整测试用例，但是他们应该运行良好。有两件事情可以归结到这里：我们有支持分页的CRUD方法，并且查询执行机制也知道`Pageable`参数。在这个阶段，我们的包装类实际上已经过时，因为客户端可能直接使用我们的存储库接口。我们摆脱了整个实施代码。

#### Summary
在本博客文章中，我们已经使用3种方法和一行XML将用于存储库的代码量减少到两个接口：

```java
@Transactional(readOnly = true) 
public interface CustomerRepository extends JpaRepository<Customer, Long> {

    Page<Customer> findByLastname(String lastname, Pageable pageable); 
}
```

```java
@Transactional(readOnly = true)
public interface AccountRepository extends JpaRepository<Account, Long> {

    List<Account> findByCustomer(Customer customer); 
}
```

```java
<jpa:repositories base-package="com.acme.repositories" />
```

我们有类型安全的CRUD方法，查询执行和内置的分页。最酷的是，这不仅适用于基于JPA的存储库，而且适用于非关系数据库。支持这种方法的第一个非关系数据库将作为Spring Data Data发布的一部分在几天内成为MongoDB。您将获得与Mongo DB完全相同的功能，并且我们也在为其他数据库提供支持。还有一些额外的功能需要探讨（例如，实体审核，自定义数据访问代码的集成），[GitHub](https://github.com/spring-projects/spring-data-examples/tree/master/jpa/showcase)有个Demo。

> **转载： 奥利弗吉尔克:<http://spring.io/blog/2011/02/10/getting-started-with-spring-data-jpa/>**



![](https://ws3.sinaimg.cn/large/006tKfTcgy1fqj5aochgoj309k09kmwz.jpg)
<b><center>扫描关注：热爱生活的大叔</center>
<b><center><font size="2">（<font size="2" color="#FF0000">转载本站文章请注明作者和出处</font> <font size="2" color="#0000FF">热爱生活的大叔-uniquezhangqi</font><font size="2">）</font>