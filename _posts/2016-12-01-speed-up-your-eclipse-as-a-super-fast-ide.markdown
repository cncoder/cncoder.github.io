---
layout:     post
title:      "【译】5个步骤加快STS运行速度（Eclipse适用）"

date:       2016-12-01 21:30:00
author:     "Romennts"
categories: Java
tags:
    - Eclipse
    - 译文
---

* content
{:toc}

> 中文网居然很少说关于STS使用的~~~Google了很多都是Eclipse，虽然差不多，但是有些竟是不通用的，所以挺坑的。

#### 跟着一下步骤你就可以得到一个相当快速的IDE，工作环境： win10 X64 8G 内存 jdk1.8

*  将你的 jdk & eclipse & workspace & STS 或者其他关于开发的文件夹加入杀毒软件的白名单

* 去除不必要的验证和启动
去除不必要验证：  Windows -> Preferences -> Validation, 点击 “Disable All”, 然后选择你需要的，我只是选择了 “Classpath Dependency Validator” .

startup action:  Windows -> Preferences, 点 “startup”, 选择不需要的启动服务

* 定义STS.ini设置Xmn ， （Eclipse是 eclipse.ini）
以下是我在8G内存电脑的设置





```

-startup
plugins/org.eclipse.equinox.launcher_1.3.200.v20160318-1642.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_64_1.1.400.v20160518-1444
-product
org.springsource.sts.ide
--launcher.defaultAction
openFile

**从下面这行开始替换，也就是-vmargs以下的所有直接换掉**
-vmargs
-Dosgi.requiredJavaVersion=1.5
-XstartOnFirstThread
-Dorg.eclipse.swt.internal.carbon.smallFonts
-server
-Xmn128m
-Xms1024m
-Xmx2048m
-Xss2m
-XX:PermSize=128m
-XX:MaxPermSize=512m
-XX:+UseParallelGC

```

* 创建内存盘（不过我觉得有上面那步已经没有必要了，所以偷懒不翻译啦）

*  确保你实在使用sun jdk

根据原文的操作，STS的速度的确有大幅度的，开启速度10S内完成，代码自动补全更加流畅了

[原文地址](http://www.beyondlinux.com/2011/06/25/speed-up-your-eclipse-as-a-super-fast-ide/)

原话：

> Make sure you are using sun jdk, not open jdk/gcj nor other jdk on linux.
If you are not satified with the after the above steps, install jrockit instead of sun jdk, and change some of the vm options by jrocket specification,
jrockit is faster than sun jdk.<br><br>
Some note:<br><br>
On my laptop(Aspire 4745G 4G mem ), windows 7 x64, by default, it takes more than 30 seconds to start eclipse.  After the vm options tuned,  takes only 15 seconds.
And after jdk moved to ram disk, it takes 10 seconds to startup.
