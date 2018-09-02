---
layout: post
title:  "docker 使用记录"
categories: node
tags:  node
author: Romennts 
---

* content
{:toc}

之前写过一篇关于docker手动安装的教程([在Ubuntu 16.04 安装docker]())，那篇写得太难了。

> Docker 官方为了简化安装流程，提供了一套安装脚本，Ubuntu 和 Debian 系统可以使用这套脚本安装：
curl -sSL https://get.docker.com/ | sh

由于GFW的原因可能会安装得极缓慢，所以国内推荐使用以下的任意一条命令安装

```shell
//阿里云的安装脚本

curl -sSL http://acs-public-mirror.oss-cn-hangzhou.aliyuncs.com/docker-engine/internet | sh -

//DaoCloud 的安装脚本

curl -sSL https://get.daocloud.io/docker | sh
```




此外，国内环境使用docker，强烈建议配置一个加速器：

一条命令即可，非常简单，先到下面网址注册登陆，我使用github直接授权就可以登陆了，

[daocloud.io 提供的加速器](https://www.daocloud.io/mirror)

然后打开：

[传送门](https://www.daocloud.io/mirror#accelerator-doc)

docker使用非常简单：

例如我想在docker使用mysql：

> docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

说明： ... where some-mysql is the name you want to assign to your container, my-secret-pw is the password to be set for the MySQL root user and tag is the tag specifying the MySQL version you want. See the list above for relevant tags.

发现物理机无法连接可以查看这个[权限配置](http://stackoverflow.com/questions/1559955/host-xxx-xx-xxx-xxx-is-not-allowed-to-connect-to-this-mysql-server)

多敲命令行就明白了，个人觉得docker和git使用相差无几。

参考：

[Docker 教程](http://www.runoob.com/docker/docker-tutorial.html)

[官方的英文写得好简单又易懂](https://docs.docker.com/)
