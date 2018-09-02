---
layout:     post
title:      "使用AWS Ubuntu14.04安装Hadoop与配置伪分布详细教程"

date:       2016-09-27 09:30:00
author:     "Romennts"
categories: 云计算
tags:
    - Hadoop
    - 大数据
---

* content
{:toc}

> 这是入坑Hadoop 几周了笔记和总结，鉴于之前一直安装不成功，而且网上的教程大多不正确，本文详细记录了如何在无界面情况下用xshell连接AWS ec2 ubuntu 搭建Hadoop 2.6 伪分布环境，以及运行自带的wordcount实例的详细步骤。我暂且假设读者对Hadoop并无太多了解，对Ubuntu的常见操作有一定了解。

## 阅读本文你需要以下知识

> Linux基本操作命令 <br>
推荐使用 [vim编辑器操作](http://www.jianshu.com/p/bcbe916f97e1) <br>
如果实在不行，本文也有大量截图以及提示，一一对应就不会错了





## 系统环境

* Ubuntu14或者以上版本

* 如果你是配置失败多次再来看本文的话，建议**重装ubuntu**再回来看本文

![我的环境](http://yicodes.com/img/install_hadoop/environment_vps.png)

## 安装过程包括

* 1.Java jdk 安装

* 2.创建Hadoop用户

* 3.配置ssh，是节点之间免密码通信

* 4.安装并配置Hadoop伪分布模式

* 5.测试运行wordcount实例

***

### 1. Java jdk 安装

Java 8 正式版 于 2014 年 3 月发布，该版本是一个有重大改变的版本，对 JAVA 带来了诸多新特性。详细信息可以参看 Java 8 新特性概述

#### 1.1安装基础开发套件

安装Ubuntu下的基础开发套件，其中包括接下来 1.2 中要用到的 add-apt-repository 命令：

```bash
sudo apt-get install software-properties-common
sudo apt-get install python-software-properties
```

#### 1.2通过ppa安装Java 8
关于ppa(Personal Package Archives)的解释可以参考什么是Ubuntu PPA以及为什么要用它

```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer

**便捷**： sudo apt-get install software-properties-common && sudo apt-get install python-software-properties && sudo apt-get update && sudo apt-get install oracle-java8-installer
```

> **便捷指的是对多条命令的合成，下同**

![java安装图解](http://yicodes.com/img/install_hadoop/install_java1.png)

#### 1.3 验证安装的Java版本
在根据以上的步骤安装了Java之后，可以通过以下命令检测是否安装成功，及安装的版本：

```bash
  java -version
```
看到和这张图一样的提示就是正确啦（这和window下安装jdk是一个样）

![java安装图解2](http://yicodes.com/img/install_hadoop/install_java2.png)


#### 1.4 通过PPA配置Java的环境
1.2中添加的Webupd8 ppa 仓库提供了一个包来设置Java的环境变量，通过以下命令安装：

```bash
sudo apt-get install oracle-java8-set-default
```

![java安装图解2](http://yicodes.com/img/install_hadoop/setjava8.png)


完成以上步骤后即已经成功的配置了安转Hadoop所需的Java开发环境。

### 2.创建Hadoop用户

为了营造一个更加独立的Hadoop运行环境，我们可以为系统新建一个Hadoop账户，**以后执行Hadoop相关操作时都以该用户的身份登陆**。

#### 2.1 新建hadoop用户


````bash
sudo useradd -m  hadoop     #-m参数表示同时创建用户的家目录 <br>
sudo usermod -s  /bin/bash hadoop    # 指定默认登陆shell<br>
sudo passwd hadoop    #修改用户默认的密码
cp authorized_keys .ssh/authorized_keys

# 便捷： export user=heamon7 && sudo useradd -m $user && sudo usermod -s /bin/bash $user && sudo passwd $user
# cp authorized_keys .ssh/authorized_keys

````


![新建hadoop用户](http://yicodes.com/img/install_hadoop/adduser.png)


#### 2.2 提升hadoop用户的权限

sudo命令可以让你切换身份来执行命令,其执行过程是：

>
* 当用户执行sudo时，系统于/etc/sudoers文件中查找该用户是否具有sudo的权限；<br>
* 当用户具有可执行sudo的权限后，便让用户输入用户自己的密码来确认；<br>
* 若密码输入成功，便开始进行sudo后续接得命令（但root执行sudo时不需要输入密码， 若欲切换的身份与执行者的身份相同，则也不需要输入密码）；


因此我们需要编辑/etc/sudoers文件，将我们的hadoop用户添加进去：

```bash
sudo chmod u+w /etc/sudoers    #为当前用户添加对/etc/sudoers文件的写权限，该文件默认root只读  
sudo vim /etc/sudoers
```

在文件中找到这两行：

> \#User privilege specification <br>
root ALL=(ALL:ALL) ALL


在其下面添加一行

```bash
hadoop  ALL=(ALL:ALL) ALL
```

**很多程序员看到用vim就懵逼了，怎么写不了，打什么都没有的呢。按i即可开始输入了，输入完成后，按Esc，在按冒号，注意是英文的冒号，然后输入wq，回车退出即可保存** 建议各位有空补一下vim的基本操作。


![提升hadoop用户的权限](http://yicodes.com/img/install_hadoop/insertdata.png)

这里解释一下这一行四个参数的意思，第一个是用户的账号，表示系统中得哪个账户可以使用sudo命令，默认是root，第二个是登录者的来源主机名，默认值root可以来自任何一台网络主机，第三个参数是可切换的身份，默认root可以切换成任何用户,若添加冒号，则表示的是用户组；第四个参数是可执行的命令，需使用绝对路径，默认root可以执行任何命令。其中ALL是特殊的关键字，表示任何主机、用户，或命令。
所以添加上面一行之后，我们的hadoop用户今后也具有和root一样的权限。
最后
最后我们撤销文件的写权限:

```bash
sudo chmod u-w /etc/sudoers  
#便捷 sudo chmod u+w /etc/sudoers && sudo vim /etc/sudoers && sudo chmod u-w /etc/sudoers
```

### 3. 配置本地ssh免登陆（重点！）

因为Hadoop是通过ssh管理各个组件，并实现通信的，为了避免每次需要输入密码，我们可以配置本地ssh免登陆。关于ssh的登陆方式及原理可以参考这里 SSH原理与运用

```bash
su  hadoop
ssh-keygen -t rsa    #用RSA算法生成公钥和密钥 <br>
cat /home/hadoop/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys    #授权本机ssh免登陆
```

![配置本地ssh免登陆](http://yicodes.com/img/install_hadoop/keygen.png)


可以通过以下命令测试是否配置成功：

```bash
ssh localhost
```

![验证本地ssh免登陆](http://yicodes.com/img/install_hadoop/authyes.png)

若配置成功，则应该不需要输入密码。接着退出ssh登陆：

```bash
exit
```

上面是假设已经安装了SSH，如果没有安装，可以通过以下命令安装并启动：

>
* sudo apt-get install openssh-server <br>
* sudo /etc/init.d/ssh start

### 4. 安装并配置Hadoop伪分布模式

**以下命令都是在hadoop用户运行**

#### 4.1 下载Hadoop 2.6

这里直接从官网下载Hadoop并放到hadoop用户主目录：

```bash
cd ~
pwd
wget http://apache.claz.org/hadoop/common/hadoop-2.6.0/hadoop-2.6.0.tar.gz
```

![确保在hadoop用户home目录](http://yicodes.com/img/install_hadoop/makesure_hadoop_home.png)

![下载hadoop到home目录](http://yicodes.com/img/install_hadoop/download_hadoop.png)


解压并简化路径：

```bash
tar xzf hadoop-2.6.0.tar.gz
mv hadoop-2.6.0 /home/hadoop/hadoop
```

![下载hadoop到home目录](http://yicodes.com/img/install_hadoop/hadoopfile.png)


#### 4.2. 配置环境变量

首先设置Hadoop运行所需要的环境变量，我们编辑 ~/.bashrc 文件，添加我们的环境变量



**从这里开始需要特别注意目录对应，如果出错，请检查HADOOP_HOME这个变量是否是对应**

```bash
\#注意此处的路径和你的hadoop文件最后解压存放的位置是一致的
export HADOOP_HOME=/home/hadoop/hadoop   
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```

然后我们重新导入~/.bashrc文件中的内容到shell中:

```bash
source ~/.bashrc
```

关于上面这条命令的执行原理，请查看[这里](http://blog.csdn.net/ithomer/article/details/6322892)和[这里](http://www.51testing.com/html/38/225738-206878.html)

> 如果不想深究请略过

接着我们编辑文件$HADOOP_HOME/etc/hadoop/hadoop-env.sh ，为Hadoop设置 Java环境变量:

```bash
vim $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```

修改其中JAVA_HOME的值为：

```bash
export JAVA_HOME=/usr/lib/jvm/java-8-oracle   
#如果按照前面安装Java 8 的方法，则Java应该在此路径，也可以通过 echo $JAVA_HOME 命令来查看
```

#### 4.3 修改Hadoop的配置文件

Hadoop在目录 $HADOOP_HOME/etc/hadoop 下有很多配置文件，我们在此配置伪分布式，需要修改其中四个配置文件。首先进入配置文件目录：

```bash
cd $HADOOP_HOME/etc/hadoop
```

然后编辑 core-site.xml 文件：

```bash
vim core-site.xml
```

在 configuration 标签里添加属性，这里需要添加的是HDFS的NameNode的地址，修改成以下值:

```xml
<configuration>
<property>
  <name>fs.default.name</name>
    <value>hdfs://localhost:9000</value>
</property>
</configuration>
```

接着修改 hdfs-site.xml 文件:

```bash
vim hdfs-site.xml
```

修改为以下值：

```xml
<configuration>
<property>
 <name>dfs.replication</name>
 <value>1</value>
</property>
<property>
 <name>dfs.name.dir</name>
 <value>file:///home/hadoop/hadoopdata/hdfs/namenode</value>
</property>
<property>
 <name>dfs.data.dir</name>
 <value>file:///home/hadoop/hadoopdata/hdfs/datanode</value>
</property>
</configuration>
```

上面的 dfs.replication 属性是指定副本数量，这里数据只保存一份，默认是三份；而 dfs.name.dir 属性指定的是NameNode在本地的保存地址，dfs.data.dir 属性指定的是DataNode在本地的保存地址。

然后修改 mapred-site.xml 文件，但是默认Hadoop只提供了该配置的模板文件，我们需要复制模板编辑：

```bash
cp mapred-site.xml.template mapred-site.xml <br>
vim mapred-site.xml
```

将该文件修改为以下值：

```xml
<configuration>
 <property>
  <name>mapreduce.framework.name</name>
  <value>yarn</value>
 </property>
</configuration>
```

从字面意义也可以看出这里指定了MapReduce运行在Hadoop的Yarn框架上。

最后修改 yarn-site.xml 文件:

```bash
vim yarn-site.xml
```

修改为以下值：

```xml
<configuration>
 <property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
 </property>
</configuration>
```

这里 yarn.nodemanager.aux-services 属性指定了Yarn的NodeManager获取数据的方式是shuffle，关于shuffle的介绍可以看这里MapReduce:[详解shuffle过程](http://langyu.iteye.com/blog/992916)

#### 4.4 启动Hadoop

在使用HDFS文件系统前，我们需要先将其格式化：

```bash
hdfs namenode -format
```

![格式化](http://yicodes.com/img/install_hadoop/runhadoop.png)

此时应该有大量日志输出......

> 如果出现日志中出现空指针异常之类的，看是否存在hadoopdata目录，如果是使用<br>
  rm -rf /home/hadoop/hadoopdata  #删除hadoopdata目录 <br>
  如果还不行就重装ubuntu吧

然后启动HDFS：


```bash
start-dfs.sh
```

此时应该有大量日志输出......
最后启动Yarn：

```bash
start-yarn.sh
```

此时应该有大量日志输出......

此时通过jps命令，应该可以看到除了jps本身外，还有5个Java进程,如下：

![格式化](http://yicodes.com/img/install_hadoop/hadoopcomp.png)

>
hadoop@ubuntu:~$ jps<br>
14049 Jps<br>
13811 ResourceManager<br>
13459 DataNode<br>
13642 SecondaryNameNode<br>
13931 NodeManager<br>
13342 NameNode<br>

### 5.测试运行wordcount实例

Hadoop自带有很多MapReduce的例子，其中一个比较和编程语言领域的 Hello World 齐名的是wordcount,能够统计出文件中每个单词出现的次数，我们可以用wordcount示例测试Hadoop环境。

#### 5.1 上传输入文件到HDFS

首先我们在本地文件系统新建几个文本文件：

>
  cd ~  <br>
  mkdir input  <br>
  echo "hello hadoop" > input/f1.txt  <br>
  echo "hello world" > input/f2.txt  <br>
  echo "hello wordcount" > input/f3.txt  <br>


然后通过以下命令上传这三个文件到HDFS：

```bash
hdfs dfs -mkdir -p /home/hadoop/input
hdfs dfs -put input/* /home/hadoop/input
```

可以通过以下命令查看上传的文件:

```bash
hdfs dfs -ls /home/hadoop/input/
```

> 值得特别注意的是：如果你的是ubuntu 64位系统会出现这样的警告：

```bash
  WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
```

> The reason you saw that warning is the native Hadoop library $HADOOP_HOME/lib/native/libhadoop.so.1.0.0 was actually compiled on 32 bit.
Anyway, it's just a warning, and won't impact Hadoop's functionalities.
Here is the way if you do want to eliminate this warning, download the source code of Hadoop and recompile libhadoop.so.1.0.0 on 64bit system, then replace the 32bit one.Steps on how to recompile source code are included here for Ubuntu:

>> 上面是stackoverflow某位大神的回答，就是说这个警告对hadoop功能没有影响，就算在下面的命令执行过程出现了，也无需理会。如果有强迫症的你要解决的话可以去看歪果仁的 [处理方法](http://stackoverflow.com/questions/19943766/hadoop-unable-to-load-native-hadoop-library-for-your-platform-warning)

#### 5.2执行wordcount示例

Hadoop 通过把MapReduce代码捆绑到 jar 文件上，然后使用以下命令运行Streaming作业：

```bash
hadoop jar <jar> [mainClass] args...
```

在 目录 \$HADOOP_HOME/share/hadoop/mapreduce/ 下有一些运行MapReduce任务所需的已编译好的jar包，其中 hadoop-mapreduce-examples-2.6.0.jar 包含了一些示例程序，wordcount便在其中，我们可以通过下面的命令来运行wordcount：

```bash
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar wordcount /home/hadoop/input  /home/hadoop/output
```

注意此处的output文件夹不应该事先存在，应该由hadoop自己创建，否则会执行失败。

#### 5.3 查看运行结果

稍后片刻，待程序运行结束，我们可以通过以下命令来查看产生的结果文件：

```bash
hdfs dfs -ls /home/hadoop/output
```

这里的输出是：

> hadoop@ubuntu:~$ hdfs dfs -ls /home/hadoop/output  
15/06/18 02:47:10 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Found 2 items  
-rw-r--r--   1 hadoop supergroup          0 2015-06-18 02:13 /home/hadoop/output/\_SUCCESS  
-rw-r--r--   1 hadoop supergroup         32 2015-06-18 02:13 /home/hadoop/output/part-r-00000  

可以通过以下命令查看最后的输出结果：

```bash
hdfs dfs -cat /home/hadoop/output/part-r-00000
```

这里看到的结果是：

> hadoop@ubuntu:~$ hdfs dfs -cat /home/hadoop/output/part-r-00000  
15/06/18 02:49:38 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable  
hadoop    1  
hello    3  
java    1  
world    1 <br> hadoop@ubuntu:~$

后记：

在学习hadoop过程中，最艰难莫过于要学会看英文查错。其实把对应的报错提示google一下都可以解决，可能对中文用户不友好，因为现在我查到的解决方法基本都是在stackoverflow上的。所以学习英文对计算机类的同学是有相当大的益处的。
