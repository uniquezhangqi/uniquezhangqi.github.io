---
layout:     post             				# 使用的布局（不需要改）
title:         java8--Lambda排序   # 标题 
subtitle:    					  				#副标题
date:       2019-02-17  					# 时间
author:     Ian                  			# 作者
header-img: img/home-bg-o.jpg	#这篇文章标题背景图片
catalog: true                        	# 是否归档
tags:                              		#标签
    - 编程之路
    - 自我总结
    - Java Basics
---

> 转载 Java8 - 使用 Comparator.comparing 进行排序 ：https://my.oschina.net/xinxingegeya/blog/2046405

## 使用外部比较器Comparator进行排序
当我们需要对集合的元素进行排序的时候，可以使用java.util.Comparator 创建一个比较器来进行排序。Comparator接口同样也是一个函数式接口，我们可以把使用lambda表达式。如下示例，

```java
package com.common;

import java.util.*;
import java.util.stream.Collectors;

public class ComparatorTest {
    public static void main(String[] args) {

        Employee e1 = new Employee("John", 25, 3000, 9922001);
        Employee e2 = new Employee("Ace", 22, 2000, 5924001);
        Employee e3 = new Employee("Keith", 35, 4000, 3924401);

        List<Employee> employees = new ArrayList<>();
        employees.add(e1);
        employees.add(e2);
        employees.add(e3);

        /**
         *     @SuppressWarnings({"unchecked", "rawtypes"})
         *     default void sort(Comparator<? super E> c) {
         *         Object[] a = this.toArray();
         *         Arrays.sort(a, (Comparator) c);
         *         ListIterator<E> i = this.listIterator();
         *         for (Object e : a) {
         *             i.next();
         *             i.set((E) e);
         *         }
         *     }
         *
         *     sort 对象接收一个 Comparator 函数式接口，可以传入一个lambda表达式
         */
        employees.sort((o1, o2) -> o1.getName().compareTo(o2.getName()));

        Collections.sort(employees, (o1, o2) -> o1.getName().compareTo(o2.getName()));

        employees.forEach(System.out::println);
    }
}


/**
 * [Employee(name=John, age=25, salary=3000.0, mobile=9922001),
 * Employee(name=Ace, age=22, salary=2000.0, mobile=5924001),
 * Employee(name=Keith, age=35, salary=4000.0, mobile=3924401)]
 */
class Employee {
    String name;
    int age;
    double salary;
    long mobile;

    // constructors, getters & setters


    public Employee(String name, int age, double salary, long mobile) {
        this.name = name;
        this.age = age;
        this.salary = salary;
        this.mobile = mobile;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public long getMobile() {
        return mobile;
    }

    public void setMobile(long mobile) {
        this.mobile = mobile;
    }

    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("Employee{");
        sb.append("name='").append(name).append('\'');
        sb.append(", age=").append(age);
        sb.append(", salary=").append(salary);
        sb.append(", mobile=").append(mobile);
        sb.append('}');
        return sb.toString();
    }
}
```

## 使用 Comparator.comparing 进行排序
### comparing 方法一
查看 Comparator 类内部实现，还有一个 comparing 方法，实现如下，

```java
    public static <T, U extends Comparable<? super U>> Comparator<T> comparing(
            Function<? super T, ? extends U> keyExtractor)
    {
        Objects.requireNonNull(keyExtractor);
        return (Comparator<T> & Serializable)
            (c1, c2) -> keyExtractor.apply(c1).compareTo(keyExtractor.apply(c2));
    }
```

其返回值是 (c1, c2) -> keyExtractor.apply(c1).compareTo(keyExtractor.apply(c2)); 一个lambda表达式，也就是一个Compator 。所以上面那个例子也可以改造成 如下，

```java
package com.common;

import java.util.*;

public class ComparatorTest {
    public static void main(String[] args) {

        Employee e1 = new Employee("John", 25, 3000, 9922001);
        Employee e2 = new Employee("Ace", 22, 2000, 5924001);
        Employee e3 = new Employee("Keith", 35, 4000, 3924401);

        List<Employee> employees = new ArrayList<>();
        employees.add(e1);
        employees.add(e2);
        employees.add(e3);

        /**
         *     @SuppressWarnings({"unchecked", "rawtypes"})
         *     default void sort(Comparator<? super E> c) {
         *         Object[] a = this.toArray();
         *         Arrays.sort(a, (Comparator) c);
         *         ListIterator<E> i = this.listIterator();
         *         for (Object e : a) {
         *             i.next();
         *             i.set((E) e);
         *         }
         *     }
         *
         *     sort 对象接收一个 Comparator 函数式接口，可以传入一个lambda表达式
         */
        employees.sort((o1, o2) -> o1.getName().compareTo(o2.getName()));

        Collections.sort(employees, (o1, o2) -> o1.getName().compareTo(o2.getName()));

        employees.forEach(System.out::println);


        /**
         * Comparator.comparing 方法的使用
         *
         * comparing 方法接收一个 Function 函数式接口 ，通过一个 lambda 表达式传入
         *
         */
        employees.sort(Comparator.comparing(e -> e.getName()));

        /**
         * 该方法引用 Employee::getName 可以代替 lambda表达式
         */
        employees.sort(Comparator.comparing(Employee::getName));

    }
}


/**
 * [Employee(name=John, age=25, salary=3000.0, mobile=9922001),
 * Employee(name=Ace, age=22, salary=2000.0, mobile=5924001),
 * Employee(name=Keith, age=35, salary=4000.0, mobile=3924401)]
 */
class Employee {
    String name;
    int age;
    double salary;
    long mobile;

    // constructors, getters & setters


    public Employee(String name, int age, double salary, long mobile) {
        this.name = name;
        this.age = age;
        this.salary = salary;
        this.mobile = mobile;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getSalary() {
        return salary;
    }

    public void setSalary(double salary) {
        this.salary = salary;
    }

    public long getMobile() {
        return mobile;
    }

    public void setMobile(long mobile) {
        this.mobile = mobile;
    }

    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("Employee{");
        sb.append("name='").append(name).append('\'');
        sb.append(", age=").append(age);
        sb.append(", salary=").append(salary);
        sb.append(", mobile=").append(mobile);
        sb.append('}');
        return sb.toString();
    }
}
```

### comparing 方法二

```java
   public static <T, U> Comparator<T> comparing(
            Function<? super T, ? extends U> keyExtractor,
            Comparator<? super U> keyComparator)
    {
        Objects.requireNonNull(keyExtractor);
        Objects.requireNonNull(keyComparator);
        return (Comparator<T> & Serializable)
            (c1, c2) -> keyComparator.compare(keyExtractor.apply(c1),
                                              keyExtractor.apply(c2));
    }
```

和comparing 方法一不同的是 该方法多了一个参数 keyComparator ，keyComparator 是创建一个自定义的比较器。

```java
Collections.sort(employees, Comparator.comparing(
                Employee::getName, (s1, s2) -> {
                    return s2.compareTo(s1);
                }));
```

## 使用 Comparator.reversed 进行排序

返回相反的排序规则，
```java
/**
 *  相反的排序规则
 */
Collections.sort(employees, Comparator.comparing(Employee::getName).reversed());

employees.forEach(System.out::println);

//输出，

Employee{name='Keith', age=35, salary=4000.0, mobile=3924401}
Employee{name='John', age=25, salary=3000.0, mobile=9922001}
Employee{name='Ace', age=22, salary=2000.0, mobile=5924001}

```

## 使用 Comparator.nullsFirst进行排序
当集合中存在null元素时，可以使用针对null友好的比较器，null元素排在集合的最前面

```java
employees.add(null);  //插入一个null元素
Collections.sort(employees, Comparator.nullsFirst(Comparator.comparing(Employee::getName)));
employees.forEach(System.out::println);


Collections.sort(employees, Comparator.nullsLast(Comparator.comparing(Employee::getName)));
employees.forEach(System.out::println);
```

## 使用 Comparator.thenComparing 排序
首先使用 name 排序，紧接着在使用ege 排序，来看下使用效果

```java
Collections.sort(employees, Comparator.comparing(Employee::getAge).thenComparing(Employee::getName));
employees.forEach(System.out::println);
```

