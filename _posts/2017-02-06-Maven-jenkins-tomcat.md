---
layout: post
title:  "使用Jenkins、Git私有服务器打造一元微信小程序持续集成环境"
categories: 运维
tags:  git Jenkins CI
author: Romennts 
---

* content
{:toc}

## 开发背景

近来使用Java作为后端语言开发微信小程序，就如你所想，不会每次更新网站还得再上传一遍war包到tomcat吧 那不折腾死，这不是我想要做的，而且这样不利于版本控制~

最近和朋友协作开发，我负责在微信小程序前端调用接口，朋友负责用Java Web开发，感觉用java web有点小题大做。Git作为版本控制，在这安利一下 AWS CodeCommit ，2月1日的gitlab事件，虽然git作为一种分布式的存在，主中心服务器丢失数据损失不大，但还是让我觉得选择靠谱的Git服务器还是很重要的，关键是AWS家的这款产品还是免费使用的，很赞。在开发过程中，使用tomcat服务器部署，每次修改代码后都要push一次，然后再在本地打包成war再上传服务器，甚是麻烦。之前看《轻量级微服务架构》这本书有说Jenkins使用，正好符合我的需求~




## 技术栈说明

* tomcat 7.0 服务器（windows server 2012） 
* Https && CDN 
* Java Web 
* Jenkins 
* Maven 
* Git

> 微信官方说明：微信小程序的服务器域名要求已经备案，且使用HTTPS，在这里HTTPS实现并不在服务器，而是使用了 又拍云CND ，当天申请，当天通过，免费，而且还是年初六，赞b(￣▽￣)d~~

## tomcat配置

端口肯定要设置成80的啦

增加user（打开tomcat的conf目录下的tomcat-users.xml添加~自动部署的时候需要用到）

java web&&Maven项目配置

工程根目录下增加pom.xml文件，我的是这样的（之前没怎么用过，只能现学现卖XD，我学maven连带写这个用了大半天）

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>mywebapp</groupId>
	<artifactId>mywebapp</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>
	<build>
		<sourceDirectory>src</sourceDirectory>
		<plugins>
			<plugin>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.1</version>
				<configuration>
					<source>1.7</source>
					<target>1.7</target>
				</configuration>
			</plugin>
			<plugin>
				<artifactId>maven-war-plugin</artifactId>
				<version>2.4</version>
				<configuration>
					<warSourceDirectory>WebContent</warSourceDirectory>
					<failOnMissingWebXml>false</failOnMissingWebXml>
				</configuration>
			</plugin>
		</plugins>
	</build>
	<dependencies>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
		</dependency>
		
		<dependency>
		<groupId>net.sf.json-lib</groupId>
    	<artifactId>json-lib</artifactId>
    	<version>2.4</version>
		<scope>system</scope>
		<systemPath>${project.basedir}/lib/json-lib-2.4-jdk15.jar</systemPath>
		</dependency>
		
		<dependency>
	    <groupId>org.jsoup</groupId>
	    <artifactId>jsoup</artifactId>
	    <version>1.8.3</version>
	    <scope>system</scope>
		<systemPath>${project.basedir}/lib/jsoup-1.8.3.jar</systemPath>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/net.sf.ezmorph/ezmorph -->
		<dependency>
	    <groupId>net.sf.ezmorph</groupId>
	    <artifactId>ezmorph</artifactId>
	    <version>1.0.6</version>
	    <scope>system</scope>
		<systemPath>${project.basedir}/lib/ezmorph-1.0.6.jar</systemPath>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
		<dependency>
	    <groupId>commons-logging</groupId>
	    <artifactId>commons-logging</artifactId>
	    <version>1.1.3</version>
	    <scope>system</scope>
		<systemPath>${project.basedir}/lib/commons-logging-1.1.3.jar</systemPath>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/commons-lang/commons-lang -->
		<dependency>
	    <groupId>commons-lang</groupId>
	    <artifactId>commons-lang</artifactId>
	    <version>2.5</version>
	    <scope>system</scope>
		<systemPath>${project.basedir}/lib/commons-lang-2.5.jar</systemPath>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/commons-collections/commons-collections -->
		<dependency>
	    <groupId>commons-collections</groupId>
	    <artifactId>commons-collections</artifactId>
	    <version>3.2.1</version>
	    <scope>system</scope>
		<systemPath>${project.basedir}/lib/commons-collections-3.2.1.jar</systemPath>
		</dependency>
		
		<!-- https://mvnrepository.com/artifact/commons-beanutils/commons-beanutils -->
		<dependency>
	    <groupId>commons-beanutils</groupId>
	    <artifactId>commons-beanutils</artifactId>
	    <version>1.8.3</version>
	    <scope>system</scope>
		<systemPath>${project.basedir}/lib/commons-beanutils-1.8.3.jar</systemPath>
		</dependency>
		
	</dependencies>
</project>
```

## Jenkins设置（重头戏来啦）

简要说一下 部署Jenkins ： 从Jenkins官方网站下载最新的war包。扔到tomcat webapp目录即可

部署完打开 http://localhost/jenkins 按要求设置就可以了。

用管理员账号登录Jenkins后，第一次使用前，需要在“系统管理”->“Global Tool Configuration”->设置jdk，Git，Maven ，我是使用系统中的，并不使用Jenkins自动安装的。如果你想使用自动安装也可以，“Maven”中新增一个Maven，直接输入一个名字，选中“自动安装”，Jenkins会自动下载并安装Maven，可是不知道为什么windows server2012下多次不成功


* 安装Jenkins插件: 系统管理 -> 管理插件 -> 可选插件， 在这里直接搜索以下插件且安装

* GIT plugin (可能已经默认安装了) 
* Maven Integration plugin 
* Deploy to container Plugin 

* 按照提示创建一个任务，选择Maven Project
* 在配置页中，源码管理选择Git，填入地址：
![](https://www.yicodes.com/img/jenkins/add_git.png)

![](https://www.yicodes.com/img/jenkins/built.png)

* MAVEN相关设置：
![](https://www.yicodes.com/img/jenkins/maven.png)

* 在执行完MAVEN命令后，我们就把打包好的war文件部署到tomcat服务器：
![](https://www.yicodes.com/img/jenkins/deploy.png)

* 保存后，就可以执行自动化构建了。

部署成功！！！ tomcat服务器看到有对应的服务正在运行

## 后记

项目的确是成功自动化部署到服务器，可是有些文件无法访问或者代码功能缺失，一般都是pom文件没有写好，打包不完整。其实jenkins更多的是写shell自动部署，但是鉴于windows那像个半成品样子的bat，还是算了。

如果你是学生，腾讯云服务器的确是1元就可以了，整个过程就这里需要付费~~阿里云好像也有，但，前几年我用过万网之后，对阿里产品甚是反感，可还是得用，例如支付宝…