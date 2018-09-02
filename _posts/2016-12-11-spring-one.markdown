---
layout:     post
title:      "Spring 入门"

date:       2016-12-11 14:45:00
author:     "Romennts"
categories: Java
tags:
    - Spring
    - 自制教程
---

* content
{:toc}

> Spring 是一个轻量级框架，不仅仅是一个IoC容器，还有很多其他例如AOP重要的思想，你可以把Spring用在各种开发，绝不是仅仅局限于Java EE，此外，AOP已经是OOP的升级版了，就像过去Java 对于 C而言，不是谁替代谁，只是所处的位置不一样而已。

#### 程序设计语言框架一般先研究生命周期

1. Spring 里面，有一大堆方法，如BeanNameAware、BeanFactoryAware、InitializingBean和DiposableBean这些接口的方法....控制着bean的生命周期

2. 然而，对于这么多的方法或者接口，有很多都不是常用的，在这里说一个常用的， BeanPostProcessor 这个接口实现类似于Java EE的过滤器，专业术语叫做后置处理器

3. 此外，用ApplicationContext 和 Factory创建的bean生命周期并不一样

4. 依赖注入默认需要运行无参构造函数

5. 因为Java的反射机制，有利于依赖注入的控制




###  在Spring里面，装配bean是重中之中

1.依赖注入（DI）一般就是在bean里面用Setter注入实现的，当然也可以用构造器注入（一般用于强制使用依赖关系），不过在实际项目中大多数都是使用前者，这样更加符合AOP的思想

2.通过<list/> 、 <set/> 、 <map/>及<props/>元素可以定义和设置与Java Collection类型对应List、Set、Map及Properties的值

> 使用集合设置属性后，常常需要获取，推荐用Entry，虽然迭代器的方法看起来高级，起码有一种你是比较熟悉。

3.bean可以相互嵌套，形成内部bean，类似于Java的内部类啦~

4.Spring Framework支持五种作用域（其中有三种只能用在基于web的Spring ApplicationContext），设置的关键字是： scope

5.Spring IoC容器可以自动装配（autowire）相互协作bean之间的关联关系。但是项目中不常用

> 官方说明： 尽管自动装配比显式装配更神奇，但是，正如上面所提到的，Spring会尽量避免在装配不明确的时候进行猜测，因为装配不明确可能出现难以预料的结果，而且Spring所管理的对象之间的关联关系也不再能清晰的进行文档化。（难得官方文档说人话XD）

6.对象继承和Java差不多，使用parent，属性可以覆盖

```
<bean id="inheritedTestBeanWithoutClass" abstract="true">
    <property name="name" value="parent"/>
    <property name="age" value="1"/>
</bean>

<bean id="inheritsWithClass" class="org.springframework.beans.DerivedTestBean"
    parent="inheritedTestBeanWithoutClass" init-method="initialize">
  <property name="name" value="override"/>
  <!-- age will inherit the value of 1 from the parent bean definition-->
</bean>

<!-- 上面的第二个name值就是覆盖了原来父类的属性了-->
```

7.装配特殊的bean中就有一个叫做分散处理的东西，可以用来处理国际化的问题，以下是Demo示例，"$"作为占位符，另外location可以引用多个文件，

```
<context:property-placeholder location="classpath:db.properties,db2.properties"/>
<bean id="db" class="com.yicodes.dbuntil.DB">
	<property name="name" value="${db2.name}" />
	<property name="dbname" value="${db2.dbname}" />
	<property name="pw" value="${pw}" />
</bean>

db2.properties 文件内容
db2.name="orcal"
db2.dbname="orcal:@mysl@3306@"
db2.pw=dsadsa
```

#### AOP(面向切面编程)

1. 为什么会存在？AOP 在什么情况下被使用

2. 越来越多的框架使用AOP思想

3. 如何使用

* keyword： method,object,target,通知，代理对象，被代理对象
> 通知又有很多种，前置，后置，引用...

4.  **代理对象** 不需要使用Java代码实现，只需要在xml文件中配置，你需要明确：
* 代理哪个接口
* 具体代理谁，对象
* 代理什么，方法
> 如果你需要弄懂这里的原理，你需要熟知Java继承（用接口实现多重继承）

##### 原文地址（转载前发邮件知会即可：romennts@gmail.com）
https://www.yicodes.com/2016/12/11/spring-one/

#### 参考资料

1. Spring揭秘
