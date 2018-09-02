---
layout:     post
title:      "为自定义域名的GitHub Pages添加SSL 完整方案 "

date:       2016-12-04 02:35:00
author:     "Romennts"
categories: 运维
tags:
    - https
    - 安全
    - 自制教程
---

* content
{:toc}

> Google宣布了，从2017年1月份正式发布的Chrome 56开始，Google将把某些包含敏感内容的https页面标记为“不安全”。

* 简单总结一下这个过程：

1. 在 github page 的设置中填入 custom domain
2. 去你的域名注册商更改 dns server 成 cloudflare 所提供的
3. 在 cloudflare 设置 A record ，[参照]( https://help.github.com/articles/setting-up-an-apex-domain/#configuring-a-records-with-your-dns-provider)
4. 在 cloudflare 配置 crypto ，选择 flexiable （这一步要一段时间才能起效）
5. 在 cloudflare 配置 page rule ，一个是 always use https ，一个是 redirect http 到 https
**每一步的操作可能需要 5-30 分钟才能起效**




#### 为什么使用Cloudflare提供的免费SSL

收费的SSL服务总是比免费的更加周到，一般收费的SSL都会提供端到端的加密。但是价格不菲，对于个人博客来说，这是一笔不必要的开销。我只是需要看到网站地址栏有绿色的锁头，那就证明我们的网站相对安全了。

此外，使用https之后，谷歌、百度等搜索排名权值（PR等）也会有相对提升。

还有其他的一些，例如Cloudflare还提供免费的CDN和缓存技术，让浏览者有更好的体验~~

> 好了，说了那么多，直接看教程~~

#### 创建CloudFlare帐户，并添加网站

**首先你已经有自己的自定义域名的GitHub Pages** ，我的 GitHub Pages cname文件写的是 yicodes.com

> 实现目标： 当访客输入 yicodes.com 强制跳转使用https，访问wwww 也会跳转到https://www.yicodes.com

* 如果你还没有Cloudflare账号，[点击注册](https://www.cloudflare.com/a/sign-up)

* 登陆后，[点击这里](https://www.cloudflare.com/a/add-site) 增加你的域名，如下图，输入你的域名，例如 **yicodes.com**  并点击 **Begin Scan**

> 注意不要写WWW前缀，大约60秒即可完成域名解析扫描。完成后点击 **Continue Setup** 继续下一步

![Add Websites](https://www.yicodes.com/img/gpssl/github-page-ssl-1.png)

* 你看到DNS记录（包括子域）列表之后，按照下图提示设置后，其中cname是为了重定向www准备的，点击 **Continue** 下一步

![DNS records](https://www.yicodes.com/img/gpssl/dnsrecord.png)

* 选择免费计划，然后下一步~

![plan](https://www.yicodes.com/img/gpssl/plan.png)

* 到你域名控制面板修改cloudflare给出的域名服务器，我这里以 **Godaddy** 为例

![updata-nameservers](https://www.yicodes.com/img/gpssl/updata-nameservers.png)

![updata-nameservers1](https://www.yicodes.com/img/gpssl/updata-nameservers1.png)

![updata-nameservers2](https://www.yicodes.com/img/gpssl/updata-nameservers2.png)

注：官方说明，**域名服务器修改最长需要72小时生效** ，用了两个域名测试，大约需要 **5~30** 分钟，看到 **Status: Active** 即可

![status](https://www.yicodes.com/img/gpssl/status.png)

#### 设置SSL

* 点击 **crypto** 菜单 , 然后设置 **Flexible** SSL ，如下图

![full_SSL](https://www.yicodes.com/img/gpssl/flexiblessl.png)

* 添加www重定向到https://www.yicodes.com

![full_SSL](https://www.yicodes.com/img/gpssl/add301.png)

* 添加自动重定向到 SSL页面

![full_SSL](https://www.yicodes.com/img/gpssl/forcessl.png)


添加SSL的教程就此完成，**一般需要5~30分钟生效！！！** 如果你有疑问，欢迎到我博客留言

##### 原文地址（转载前发邮件知会即可：romennts@gmail.com）

**https://www.yicodes.com/2016/12/04/free-cloudflare-ssl-for-custom-domain/**

#### 参考资料
1. [cloudflare官方使用指南](https://blog.cloudflare.com/secure-and-fast-github-pages-with-cloudflare/)
1. [goyllo Blog](https://www.goyllo.com/github/pages/free-cloudflare-ssl-for-custom-domain/)
2. [keanulee Blog](https://blog.keanulee.com/2014/10/11/setting-up-ssl-on-github-pages.html)
3. [sheharyar Blog](https://sheharyar.me/blog/free-ssl-for-github-pages-with-custom-domains/)

#### 欢迎捐赠

* 只有微信支付~~赶紧黑一波支付宝全家桶

![欢迎捐赠](https://www.yicodes.com/img/wechatpay.png)


#### 后记

这几天都在忙着做一些一直没有完成的Flag，例如深入了解docker以及本文的将网站升级到https，这其中面对很多的技术问题，当然是Google，可是发现在寻找答案的时候发现很多中文博客就像写流水一样，千篇一律，形成互相抄袭的恶循环，这就照成了在一些比较新的领域出现的问题，整个中文网，来来去去都是那几篇文章，如果一直纠结在中文网的圈子里，将很难获得正确的答案。于是我尝试把中文关键字改成全英关键字，找到许多相关的文章，再结合我那蹩脚的英文，还有很6的Google Translate 总能找到答案。

诚然，全英文章对我这大三还没有过四级的来说，这将是非常恶心的一件事，Chrome 的每一个标签都是英文，边看边用机器翻译，非常吃力，还有由于语法问题理解相反了，兜个大圈，但是我想到好的方法，画思维导图、画流程图、翻译对应的方法到博客，写下总结，不知不觉，问题一个个搞定，还留下有用的笔记，方便他人使用。

**总之** ，不认识英文的软件工程师都不会有很大进步吧。当然我希望天朝的软件行业能早日处于世界之巅。
