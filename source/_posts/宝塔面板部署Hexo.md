---
title: 宝塔面板部署Hexo
tags:
  - 宝塔
  - Hexo
categories:
  - 网站建设
abbrlink: b45d67d9
date: 2022-01-19 14:32:26
---
因为现在还是学生，可以购买阿里云的学生机。

于是购买了3个月的阿里云服务器ESC，准备撸来玩玩。之前看了好多教程，想直接搭建Git和Nginx环境，然后慢慢配置，然而每次都有报错的地方，谷歌了好久还是没有解决。最后想利用宝塔面板试试。宝塔已经预装了Git环境，只需要安装Nginx即可。

<!-- more -->





宝塔系统依托Centos开发，所以我们服务器系统选择Centos7+

## 安装宝塔

```bash
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

安装完成后，登录宝塔，首次登陆会提示我们安装环境，我们勾选Nginx即可。

之后，我们在home文件夹下新建git，hexo这两个文件夹。



![image](https://vip2.loli.io/2022/01/27/c9u8DNSefF1Wsj2.jpg)                                

**随后，我们通过阿里云或者xshell登录我们的服务器，依次执行**

```
cd ..   //这里是因为默认执行目录是root，需要返回根目录
cd home
cd git
git init --bare hexoBlog.git
```

接着，在目录 `/home/git/hexoBlog.git/hooks`下新建文件post-receive，写入以下代码：

```
#!/bin/bash
git --work-tree=/home/hexo --git-dir=/home/git/hexoBlog.git checkout -f
```

注意！这里的文件不要在宝塔里直接新建，需要通过连接服务器使用命令新建：

```
cd /home/git/hexoBlog.git/hooks  
vim post-receive
```

**给post-receive权限**

```
chmod +x /home/git/hexoBlog.git/hooks/post-receive
```

## 配置Nginx

宝塔面板默认的nginx配置文件在根目录->www->serve->nginx->conf下，找到nginx.conf，编辑它，如图：

![image](https://vip2.loli.io/2022/01/27/xd2zNkWqCjwSZU8.jpg)

重启Nginx服务

```
service nginx restart
```

## 本地Hexo配置

按照这个格式配置，如果你只推送到aliyun就配置那一行就行了，推送到多个平台务必按照以下格式进行填写（注意缩进）

```
deploy:
  type: git
  repo:
      github: git@github.com:HowarZheng/howarzheng.github.io.git,master
      coding: https://git.dev.tencent.com/xigzheng/xigzheng.git,master
      #gitee: https://gitee.com/howarzheng_001/howarzheng_001.git,master
      aliyun: root@120.55.161.99:/home/git/hexoBlog
```

这时候已经完成一大半了，`hexo clean&&hexo g&&hexo d`部署后已经可以通过域名访问你的Hexo博客了。

**接下来在宝塔面板里添加网站：**

![image](https://vip1.loli.io/2022/01/27/qDV3ekwMTG6A1jn.jpg)



网站目录选择`/home/hexo`

之后在阿里云将域名解析到服务器ip即可。

![image](https://vip1.loli.io/2022/01/27/9oBmZsSu8iAbXcD.jpg)

## SSL证书申请

宝塔可以免费申请SSL证书，并进行强制`https`访问：

![image](https://vip2.loli.io/2022/01/27/wct4FXZaD6krbEO.jpg)



> 参考文献：

1. [腾讯云使用宝塔面板部署Hexo](https://www.leaflag.cn/2019/02/22/腾讯云部署Hexo/#正式开始！配置Git！)
2. [阿里云Centos7+Nginx部署Hexo静态博客](https://www.jianshu.com/p/0f9dfa9c141b)
