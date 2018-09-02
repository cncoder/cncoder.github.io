---
layout:     post
title:      "学习Java并发&&Spring前提知识"

date:       2016-11-16 20:30:00
author:     "Romennts"
categories: Java
tags:
    - Java
    - Spring
    - 并发编程
---

* content
{:toc}


> 本文不是教程，更加偏向于一个知识体系~~
  有些会直接给出对应的关键字，如果不了解自行Google




### I/O操作

[前提概要：深入分析 Java I/O 的工作机制](https://www.ibm.com/developerworks/cn/java/j-lo-javaio/)

> 无论是什么软件都一定会有IO操作，除非你写的是Hello World

1. 字节/字符/磁盘/网络  I/O操作接口

1. 字节与字符区别以及相互转换

1. AIO、NIO又是何物

1. 缓存操作以及其背后的机制

1. 常见的I/O优化方案

[最佳实践：深入分析 Java 中的中文编码问题](http://www.ibm.com/developerworks/cn/java/j-lo-chinesecoding/)

### 集合的理解与应用

[使用泛型和并发改善集合](http://www.ibm.com/developerworks/cn/java/j-collections.html)

> 众所周知，集合的用处，集合在 Java 编程中是如此重要，但你确定用对了吗?对于本文主题，集合是连接各个知识的中心

* 才疏学浅，没有深刻认识~待认识....

[最佳实践：关于 Java Collections API 您不知道的 5 件事，第 1 部分](http://www.ibm.com/developerworks/cn/java/j-5things2.html)

### 序列化

[关于 Java 对象序列化您不知道的 5 件事](http://www.ibm.com/developerworks/cn/java/j-5things1/index.html)

> 摘自大神：数年前，当和一个软件团队一起用 Java 语言编写一个应用程序时，我体会到比一般程序员多知道一点关于 Java 对象序列化的知识所带来的好处。

* 序列化的思想是 “冻结” 对象状态，传输对象状态（写到磁盘、通过网络传输等等），然后 “解冻” 状态，重新获得可用的 Java 对象

* 反序列化

* 序列化并不安全

**反射、泛型与注解属于高级Java技术，在这我简略点出一些知识点，方便理解Spring原理**

### 反射

[应用反射](https://www.ibm.com/developerworks/cn/java/j-dyn0715/)

### 泛型

[Java 泛型进阶](http://gold.xitu.io/entry/574a96b4f38c840069c53560)

### 注解与配置

[一分钟秒懂注解](https://dreamerhome.github.io/2016/08/02/annotaion/)

#### Java EE核心知识

[JavaEE 全面简介](http://www.ibm.com/developerworks/cn/java/j2ee/)

> ssh框架均起源于TA，下面介绍来源于IBM

#### J2EE的核心API与组件

> J2EE平台由一整套服务（Services）、应用程序接口（APIs）和协议构成，它对开发基于Web的多层应用提供了功能支持，下面对J2EE中的13种技术规范进行简单的描述(限于篇幅，这里只能进行简单的描述):

1. JDBC(Java Database Connectivity): JDBC API为访问不同的数据库提供了一种统一的途径，象ODBC一样，JDBC对开发者屏蔽了一些细节问题，另外，JDCB对数据库的访问也具有平台无关性。

2. JNDI(Java Name and Directory Interface): JNDI API被用于执行名字和目录服务。它提供了一致的模型来存取和操作企业级的资源如DNS和LDAP，本地文件系统，或应用服务器中的对象。

3. EJB(Enterprise JavaBean): J2EE技术之所以赢得某体广泛重视的原因之一就是EJB。它们提供了一个框架来开发和实施分布式商务逻辑，由此很显著地简化了具有可伸缩性和高度复杂的企业级应用的开发。EJB规范定义了EJB组件在何时如何与它们的容器进行交互作用。容器负责提供公用的服务，例如目录服务、事务管理、安全性、资源缓冲池以及容错性。但这里值得注意的是，EJB并不是实现J2EE的唯一途径。正是由于J2EE的开放性，使得有的厂商能够以一种和EJB平行的方式来达到同样的目的。

4. RMI(Remote Method Invoke): 正如其名字所表示的那样，RMI协议调用远程对象上方法。它使用了序列化方式在客户端和服务器端传递数据。RMI是一种被EJB使用的更底层的协议。

5. Java IDL/CORBA: 在Java IDL的支持下，开发人员可以将Java和CORBA集成在一起。 他们可以创建Java对象并使之可在CORBA ORB中展开, 或者他们还可以创建Java类并作为和其它ORB一起展开的CORBA对象的客户。后一种方法提供了另外一种途径，通过它Java可以被用于将你的新的应用和旧的系统相集成。

5. JSP(Java Server Pages): JSP页面由HTML代码和嵌入其中的Java代码所组成。服务器在页面被客户端所请求以后对这些Java代码进行处理，然后将生成的HTML页面返回给客户端的浏览器。

6. Java Servlet: Servlet是一种小型的Java程序，它扩展了Web服务器的功能。作为一种服务器端的应用，当被请求时开始执行，这和CGI Perl脚本很相似。Servlet提供的功能大多与JSP类似，不过实现的方式不同。JSP通常是大多数HTML代码中嵌入少量的Java代码，而servlets全部由Java写成并且生成HTML。

6. XML(Extensible Markup Language): XML是一种可以用来定义其它标记语言的语言。它被用来在不同的商务过程中共享数据。XML的发展和Java是相互独立的，但是，它和Java具有的相同目标正是平台独立性。通过将Java和XML的组合，您可以得到一个完美的具有平台独立性的解决方案。

7. JMS(Java Message Service): MS是用于和面向消息的中间件相互通信的应用程序接口(API)。它既支持点对点的域，有支持发布/订阅(publish/subscribe)类型的域，并且提供对下列类型的支持：经认可的消息传递,事务型消息的传递，一致性消息和具有持久性的订阅者支持。JMS还提供了另一种方式来对您的应用与旧的后台系统相集成。

7. JTA(Java Transaction Architecture): JTA定义了一种标准的API，应用系统由此可以访问各种事务监控。

8. JTS(Java Transaction Service): JTS是CORBA OTS事务监控的基本的实现。JTS规定了事务管理器的实现方式。该事务管理器是在高层支持Java Transaction API (JTA)规范，并且在较底层实现OMG OTS specification的Java映像。JTS事务管理器为应用服务器、资源管理器、独立的应用以及通信资源管理器提供了事务服务。

8. JavaMail: JavaMail是用于存取邮件服务器的API，它提供了一套邮件服务器的抽象类。不仅支持SMTP服务器，也支持IMAP服务器。

9. JTA(JavaBeans Activation Framework): JavaMail利用JAF来处理MIME编码的邮件附件。MIME的字节流可以被转换成Java对象，或者转换自Java对象。大多数应用都可以不需要直接使用JAF。

[最重要的 Java EE 最佳实践](http://www.ibm.com/developerworks/cn/websphere/techjournal/0701_botzum/0701_botzum.html)

### Java 安全技术(作为软件工程师基本修养吧)

[概要：网络安全和密码学的基本概念，而这些是非常简单，极易掌握，但又是很重要的](http://www.ibm.com/developerworks/cn/java/jw-0428-security/index.html)

[最佳实践：对 Atom 进行签名，加密和解密](http://www.ibm.com/developerworks/cn/xml/x-atomencryption/)

### Http协议（包括封装）

[关于HTTP协议，一篇就够了](http://www.jianshu.com/p/80e25cb1d81a)

* 封装HTTP协议


### 罗列涉及到的源码包

* java.lang.Stringjava.lang.Integer
* java.lang.Longjava.lang.Enum
* java.net
* java.util.HashMap  
* java.util.LinkedList
* java.util.LinkedHashMap
* java.util.TreeMapjava.util.HashSet
* java.util.LinkedHashSet
* java.util.TreeSet

* Java反射与javassist
> 反射与工厂模式 java.lang.reflect.*

* Java序列化
> java.io. Serializable

---

### 更多参阅

* [Java 多线程与并发编程专题](http://www.ibm.com/developerworks/cn/java/j-concurrent/?ca=j-r)

> 汇集了与 Java 多线程与并发编程相关的文章和教程，帮助读者理解 Java 并发编程的模式及其利弊，向读者展示了如何更精确地使用 Java 平台的线程模型。

[Java 安全专题](http://www.ibm.com/developerworks/cn/java/j-security/?ca=j-r#JAVAZ安16)
