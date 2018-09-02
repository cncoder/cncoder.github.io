---
layout:     post
title:      "AWS CodeCommit代码托管服务使用与各区域速度对比"
date:       2016-12-14 18:35:00
author:     "Romennts"
categories: Git
tags:
    - AWS
    - Git
---

* content
{:toc}

> 因为自己在做的一个项目并不打算开源，而又不想自己搭建Git Server，了解之下，AWS的CodeCommit服务5 位活动用户内是免费的，小型项目的确是不错的选择。

* CodeCommit免费账户配额

1. 无限存储库
2. 50 GB -的月存储量
3. 10,000 个 Git 请求/月




#### 使用方法

* 和Github差不多，不一样的是你需要使用
**[IAM](https://console.aws.amazon.com/iam/home?#home)** 管理使用成员，虽然不是强制，但是AWS强烈要求这样做，commit的时候也方便识别是谁~方便管理

![use](https://www.yicodes.com/img/use-aws.png)

> 说明一下: AWS把主用户认为是root用户，root用户不能使用SSH方式连接代码库，只能使用HTTPS，IAM 的user就可以用SSH

* 官方的说明很简单而且很好用：

##### Prerequisites**

1. Install Git (1.7.9 or later supported) without the Git Credential Manager utility. If you don’t have Git installed, install it now.
2. At the command line, type aws configure and configure the AWS CLI with your IAM user access key and secret key.
3. Attach an appropriate AWS CodeCommit managed policy to the IAM user. Learn more

##### Steps to clone your repository**

1. At the command line, paste the following commands:

2. git config --global credential.helper "!aws codecommit credential-helper $@"
git config --global credential.UseHttpPath true
Clone your repository to your local computer and start working on code:

3. git clone https://git-codecommit.us-west-2.amazonaws.com/v1/******


* 简单点就是上传公钥到对应的IAM账号，然后在本地设置你的config文件即可

#### 速度对比

目前AWS CodeCommit一共在四个区域开放了这项服务，下面说一下我用广州电信提交代码到不同区域的对比(没有使用代理)

* 美国东部 (弗吉尼亚北部)： 速度最快，响应速度大概是1秒内

* 美国东部 (俄亥俄)： 美区最慢，明显感觉到和美区另外两个服务区域的区别

* 美国西部 (俄勒冈)： 次之，大概是2S内，可能和文件多少有关，因为是纯手工、第六感，不是非常准确

* 欧洲 (爱尔兰)： 基本可以忽略这个区域，非常慢，比Github还慢.....

##### 原文地址（转载前发邮件知会即可：romennts@gmail.com）
https://www.yicodes.com/2016/12/14/use-aws-codecommit/

#### 参考资料

1. [AWS CodeCommit 使用指南 包括IAM权限控制]( http://docs.aws.amazon.com/codecommit/latest/userguide/access-permissions.html?icmpid=docs_acc_console_connect#access-permissions-managed-policies)

2. [AWS IAM使用指南](http://docs.aws.amazon.com/zh_cn/IAM/latest/UserGuide/introduction_identity-management.html)
