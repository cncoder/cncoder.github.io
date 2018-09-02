---
layout:     post
title:      "在Ubuntu 16.04 安装docker "

date:       2016-12-02 18:35:00
author:     "Romennts"
categories: 运维
tags:
    - docker
    - ubuntu
---

* content
{:toc}

> 由于GFW的存在，如果不使用代理的方法安装docker将会非常慢，以下是在虚拟机中可以使用物理机的shadowsock代理安装



#### 更改ubuntu源

1.软件包管理中心

在软件包管理中心“软件源”中选择“中国的服务器”下mirros.aliyun.com即可自动使用

2.安装vim(个人比较喜欢vim，你也可以使用其他的编辑器，默认vi)

> sudo apt-get install vim

#### 通过主机shadowsocks代理上网

1. 在宿主机windows上运行shadowsocks.exe并勾选“允许局域网连接”

2. 使用桥接方式运行虚拟机（这时虚拟机与宿主处于同一个局域网）

3. 进入ubuntu系统，System Settings – Network – Network proxy勾选Manual（手动）,地址全部填宿主机IP（局域网网段），设置好代理端口（可在windows下的shadowsocks查看，一般为默认1080）

4. ubuntu用浏览器访问www.google.com，成功




**如果你的物理机是出于多重网络，很有可能桥接不成功，请禁用物理机上不必要的网络**

#### 安装docker

1.更新APT的源，安装https和ca证书的库，默认这2个库都已经装了。

> sudo apt-get update <br>
  sudo apt-get install apt-transport-https ca-certificates

2.添加秘钥GPG到APT配置中

> sudo apt-key adv \ <br>
               --keyserver hkp://ha.pool.sks-keyservers.net:80 \ <br>
               --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

3.增加Docker的源到/etc/apt/souces.list文件中，我的版本是Ubuntu 16.04对应Xenial 16.04 (LTS)

> sudo vim /etc/apt/sources.list <br>
  增加到最后一行   <br><br>
  deb https://apt.dockerproject.org/repo ubuntu-xenial main

其他按照版本类似的添加就可以了



  Ubuntu version	| Repository
  -------- | ---
  Precise 12.04 (LTS)	| deb https://apt.dockerproject.org/repo ubuntu-precise main
  Trusty 14.04 (LTS)	| deb https://apt.dockerproject.org/repo ubuntu-trusty main
  Wily 15.10	| deb https://apt.dockerproject.org/repo ubuntu-wily main
  Xenial 16.04 (LTS)	| deb https://apt.dockerproject.org/repo ubuntu-xenial main



4.接下来，就可以用可以用apt-get直接安装Docker了

> sudo apt-get update  <br>
  sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual  <br>
  sudo apt-get install docker-engine

5.安装完成，默认会启动Docker

```

# 检查docker服务
~$ service docker status
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Fri 2016-12-02 10:02:38 PST; 10min ago
     Docs: https://docs.docker.com
 Main PID: 6311 (dockerd)

# 检查docker进行
~$ ps -aux|grep docker
root       6311  0.0  1.9 353564 40356 ?        Ssl  10:02   0:00 /usr/bin/dockerd -H fd://
root       6323  0.0  0.5 142672 10316 ?        Ssl  10:02   0:00 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --shim docker-containerd-shim --metrics-interval=0 --start-timeout 2m --state-dir /var/run/docker/libcontainerd/containerd --runtime docker-runc

# 检查docker版本
~$ sudo docker version
Client:
 Version:      1.12.3
 API version:  1.24
 Go version:   go1.6.3
 Git commit:   6b644ec
 Built:        Wed Oct 26 22:01:48 2016
 OS/Arch:      linux/amd64

Server:
 Version:      1.12.3
 API version:  1.24
 Go version:   go1.6.3
 Git commit:   6b644ec
 Built:        Wed Oct 26 22:01:48 2016
 OS/Arch:      linux/amd64


```

6.检查Docker是否成功，运行hello-world。如果出现下面的信息，表示Docker引擎安装成功

![安装成功](https://raw.githubusercontent.com/cncoder/cncoder.github.io/master/img/install-linux-docker.jpg)

**如果出现提示网络连接超时，请将虚拟机网络连接模式改回NAT模式，原因还不太清楚～**

> 注意：我们在执行上面的命令的时候，经常会遇到一个错误。Cannot connect to the Docker daemon. Is the docker daemon running on this host?
比如，直接输入 docker run hello-world 命令。

```

~ docker run hello-world
docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.

See 'docker run --help'.
```

**这是由于权限的问题，docker默认和root权限绑定，如果不加sudo时则没有权限。**

#### 备用方法

**至此，本教程结束，如果你还是不能成功我推荐你通过执行下面的命令，高速安装Docker**

> curl -sSL https://get.daocloud.io/docker | sh  <br>
适用于Ubuntu，Debian,Centos等大部分Linux，会3小时同步一次Docker官方资源



#### 参考资料
1. [docker官网](https://docs.docker.com/engine/installation/linux/ubuntulinux/#/install)
2. [Ali-OSM](http://mirrors.aliyun.com/help/ubuntu)
3. [Dasein’s Blog](http://www.daseinpwt.com/2015/12/03/vmware%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%80%9A%E8%BF%87%E4%B8%BB%E6%9C%BAshadowsocks%E4%BB%A3%E7%90%86%E4%B8%8A%E7%BD%91/)
