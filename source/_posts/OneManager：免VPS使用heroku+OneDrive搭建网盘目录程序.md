---
title: OneManager：免VPS使用heroku+OneDrive搭建网盘目录程序
tags:
  - VPS
  - heroku
  - OneDrive
categories:
  - 网站建设
  - 	OneDrive
date: 2022-03-03 18:32:26
---

> 本文转载自：https://www.xtboke.cn/CodeNotes/616.html
>
> 采用 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.zh) 授权协议。

# 准备工作

heroku官方： [heroku.com](http://heroku.com/)
OneManager的：https://github.com/qkqpttgf/OneManager-php

<!-- more -->

# 教程开始

1.通过heroku官网注册账户并登陆（验证码需过墙才能显示）
![3HO7jI.png](https://vip1.loli.io/2022/03/04/aWkZ8sBSCAbghE9.png)

2.点击OneManager的：github仓库地址进入后下拉找到部署到heroku然后单击按钮
![3HOzCQ.png](https://vip2.loli.io/2022/03/04/QUwVHyuMpc9gjAn.png)

3.点击后跳转到创建新应用（Create New App）页面输入应用名称（App name）以及选择地区（Choose a region）。名称随便输入（生成后分配的二级域名就是你的应用名称）地区可选美国或者欧洲。
![3HXiD0.png](https://vip2.loli.io/2022/03/04/6ysPEcUolJbkIgm.png)

4.等待部署完成后进入管理应用程序
![3HXn29.png](https://vip1.loli.io/2022/03/04/Z4uW8cUdMTBAJpn.png)

5.点击开启应用程式会打开你程序的网址，比如我在创建应用的是名称是odjc我的网站就是odjc.herokuapp.com。注意记录你的网站。
![3HXQDx.png](https://vip1.loli.io/2022/03/04/UmCYRL4E6rwzoNa.png)

6.打开你的应用程序网站后开始程序安装
![3HX8UO.png](https://vip1.loli.io/2022/03/04/ZpP1WAaKJnmC2OX.png)

7.选择中文后点击新建ApiKey进入新打开页面后复制API密钥返回安装页面填入。Set admin password为设置你网盘程序后台的密码。

![img](https://vip2.loli.io/2022/03/04/tyjMIW4isSBerHC.jpg)
![3HXt8H.png](https://vip1.loli.io/2022/03/04/yClz61BIMAg9HfN.png)

8.完成安装后进入网盘界面，点击登录并输入上一步自己设置的密码。登录后点击管理---设置进入设置页面。
![3HXwrt.png](https://vip1.loli.io/2022/03/04/tD5CpBIYZPwVhWQ.png)

9.设置页面可以设置各项设置，点击最下方进入添加Onedrive盘
![3HX2xs.png](https://vip2.loli.io/2022/03/04/m3PGdwv2QExHTAJ.png)

10.此程序可添加多盘，标签以及显示名称可用于区分。Onedrive_Ver选择默认即可，世纪互联版选择第二项，如果是开发者E5想调用APi可以用第三种自己申请应用ID与机密可调用OnedriveApi。
![img](https://vip2.loli.io/2022/03/04/7HFlRJgqEAQayTu.jpg)

11.添加后进入网盘程序首页，如需添加其他网盘再次进入设置添加

# 关于绑定自己域名的教程

1.在heroku后台找到创建的app
![3HXbRJ.png](https://vip2.loli.io/2022/03/04/oCSR4DcxYhFdi5f.png)

2.点击设定值（Settings）下拉找到新增网域（Add domain）
![3HXqz9.png](https://vip1.loli.io/2022/03/04/njtur4IMTUPSsp2.png)

3.输入域名
![3HXxZ6.png](https://vip1.loli.io/2022/03/04/MWICKDmetLfw7EH.png)

4.输入域名下一步后如果出现下图提提示点击第二个链接
![3HjCJe.png](https://vip2.loli.io/2022/03/04/pJyNcP2ULtlbHFr.png)

5.进入新页面后会要求绑定一张卡，经测试信用卡，中行跨境通，虚拟卡都可以。不用收费功能不会扣款，建议使用跨境通或者虚拟卡（添加后除了可以自定义域名外免费的使用量由550免费动态增加至1000免费动态）
![3HjAsI.png](https://vip1.loli.io/2022/03/04/AzFMvW3K85NfTGw.png)

6.完成卡验证后返回第3步添加域名后得到CNAME的解析目标。去你域名添加得到的CNAME解析后域名就添加好了
![3HjQzj.png](https://vip2.loli.io/2022/03/04/1GprDLi6bjAZxeq.png)

# 反向代理,自选cloudflare节点

由于heroku不绑定信用卡，就不能自定义域名。我觉得在heroku上绑卡没必要。所以我利用了cloudflare的workers功能实现了自定义域名。

首先你需要把域名添加进cloudflare，有两种方式，一种是通过dns接入，这种方式完全把域名交给cloudflare了。另一方式是通过cloudflare Partners的方式，这种方式可以不用dns接入。我是用的是[萌精灵](https://cdn.moeelf.com/)

- 进入萌精灵，登录你的cloudflare账号，添加域名。
- 添加好域名后就需要进入[cloudflare](https://www.cloudflare.com/),进入你刚才添加的域名中，找到`workers`->`manager workers`，第一次要叫你设置一个域名`你需要设置的前缀（默认为你邮箱前缀）.workers.dev`

[![img](https://vip2.loli.io/2022/03/04/BoIsCXQzwE26npY.png)](https://www.nbmao.com/wp-content/uploads/2020/03/eae64-20200306134315.png)

- 点`create a worker`

[![img](https://vip2.loli.io/2022/03/04/xbLZM72ujFkSBIN.png)](https://www.nbmao.com/wp-content/uploads/2020/03/537a4-20200306134429.png)

- 将下面的代码加入左边方框中，注意修改为自己的app名称

```
addEventListener(
 "fetch",event => {
 let url=new URL(event.request.url);
 url.hostname="应用名称.herokuapp.com";
 let request=new Request(url,event.request);
 event. respondWith(
 fetch(request)
 )
 }
)
```

[![img](https://vip2.loli.io/2022/03/04/XPHmzfgISeqKUtu.png)](https://www.nbmao.com/wp-content/uploads/2020/03/d4c32-20200306134915.png)

- 完成后返回这里，点击添加`route`

[![img](https://vip2.loli.io/2022/03/04/BoIsCXQzwE26npY.png)](https://www.nbmao.com/wp-content/uploads/2020/03/eae64-20200306134315.png)

- 添加一个`route`,格式为`前缀.你的域名/*`，比如`pan.gyh.im/*`,worker选择你刚才创建的

[![img](https://vip1.loli.io/2022/03/04/P4ACmfSpItnoa2F.png)](https://www.nbmao.com/wp-content/uploads/2020/03/4fe71-20200306135459.png)

- 回到萌精灵中添加一个cname记录，将你添加的route域名，解析到分配的workers域名中。
- 然后在你的域名dns提供商哪里，添加下面的解析记录

[![img](https://vip1.loli.io/2022/03/04/EsWA6ejTQu2HapY.png)](https://www.nbmao.com/wp-content/uploads/2020/03/7d386-20200306140521.png)

- 最后你可以指定clodflare的访问节点，不需要用cloudflare分配的节点了，分配的节点一般较慢。只需再添加一个A记录，比如我这里是添加的`pan.gyh.im` A记录到`1.0.0.1`
- 具体可以指定到哪些节点可参考[这里](https://ofvps.com/201907510)

```
适合电信的节点
104.23.240.*
走欧洲各国出口 英国德国荷兰等 延迟比美国高一些 适合源站在欧洲的网站
172.64.32.*
虽然去程走新加坡，但是回程线路的绕路的，实际效果不好，不推荐
104.16.160.*
圣何塞的线路，比洛杉矶要快一点，推荐
108.162.236.*
亚特兰大线路，延迟稳定，但是延迟较高

适合移动的节点
162.158.133.* 走的丹麦，这一段ip只有部分能用，可以自己试一下。绕美国。
198.41.214.*
198.41.212.*
198.41.208.*
198.41.209.*
172.64.32.*
141.101.115.*
移动走香港的IP段有很多，以上并不是全部。CF移动走香港的分直连和走ntt的效果都挺不错的，不过部分地区晚上还是会丢包。
172.64.0. 这是走圣何塞的，一般用香港的就行
172.64.16.* 欧洲线路.绕

1.0.0.1效果较好
电信部分
大多数省直接使用1.0.0.0即可，延迟低，丢包少，
少部分还是需要换ip
```

# https访问

添加如下页面规则

```
http://domain/*`也可换成`domain/*
```

![img](https://vip2.loli.io/2022/03/04/woiaQeIU1OWTBh6.png)
