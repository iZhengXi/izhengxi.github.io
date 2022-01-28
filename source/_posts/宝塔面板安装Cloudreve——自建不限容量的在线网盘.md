---
title: 宝塔面板安装Cloudreve——自建不限容量的在线网盘
date: 2022-01-19 14:32:13
tags:
- 宝塔
- clouddreve
- 网盘
categories:
- 网站建设
---
Github：https://github.com/cloudreve/Cloudreve

> 官方文档：https://docs.cloudreve.org/
>
> **部署环境：CentOS 7.6/Nginx -Tengine2.2.4/MySQL 5.7.30**

## 安装

1.Releases下载程序到本地：

```bash
wget https://github.com/cloudreve/Cloudreve/releases/download/3.1.1/cloudreve_3.1.1_linux_amd64.tar.gz
```

在 /home 目录新建一个程序文件夹，方便日后文件管理：

```bash
mkdir /home/cloudreve
```

将下载的程序解压到 /home/cloudreve 目录

```bash
tar -C /home/cloudreve -xzf cloudreve_3.1.1_linux_amd64.tar.gz
```

进入程序目录赋予执行权限，并启动

```bash
# 进入程序目录
cd /home/cloudreve
# 赋予执行权限
chmod +x ./cloudreve
# 启动 Cloudreve
./cloudreve
```

不出意外的话会跳出程序初始化界面，记得保存账号密码。

![image](https://vip2.loli.io/2022/01/27/8ISaRh5cDn7LtNY.jpg)

放行 5212 端口（我是宝塔，后台添加放行端口即可 )。访问 http://ip:5212 看看程序是否正常开启，同时 Shell 也会跑出记录。
![image](https://vip2.loli.io/2022/01/27/P1oWftS8Uv2u7dD.jpg)
![image](https://vip2.loli.io/2022/01/27/vHADRozQXK8ShP4.jpg)


确认无误后，Shell 面板 Ctrl+ C 结束程序运行, 配置 Systemd 进程守  

```
vim /usr/lib/systemd/system/cloudreve.service
```

根据实际情况填写以下内容并保存：

```bash
[Unit]
Description=Cloudreve
Documentation=https://docs.cloudreve.org
After=network.target
Wants=network.target
[Service]
WorkingDirectory=/home/cloudreve
ExecStart=/home/cloudreve/cloudreve
Restart=on-abnormal
RestartSec=5s
KillMode=mixed
StandardOutput=null
StandardError=syslog
[Install]
WantedBy=multi-user.target
```

其中以下配置项需要根据实际情况更改：

- **WorkingDirectory** 主程序所在目录

- 设置开机启动

  ```bash
  systemctl enable cloudreve
  ```

  

日后你可以通过以下指令管理 Cloudreve 进程：

```bash
# 启动服务
systemctl start cloudreve
# 停止服务 
systemctl stop cloudreve
# 重启服务 
systemctl restart cloudreve
# 查看状态 
systemctl status cloudreve
```

最后 Nginx 反代一波，宝塔添加一个站点，解析好域名, 站点修改添加反向代理:

![image](https://vip2.loli.io/2022/01/27/8ISaRh5cDn7LtNY.jpg)

添加完反代后便可以通过自己的域名访问了，程序的部署到这里也算基本完成了。

## 补充

接下来进行一些小调整，Cloudreve 默认数据库内置的 SQLite，个人还是喜欢 Mysql。

Tips: 更换数据库配置后，Cloudreve 会重新初始化数据库，原有的数据将会丢失。

宝塔创建一个数据库，然后编辑 Cloudreve 的配置文件：

```bash
vim /home/cloudreve/conf.ini
```

添加以下配置：

```bash
[Database]
; 数据库类型，目前支持 sqlite | mysql
Type = mysql
; 用户名
User = root
; 密码
Password = root
; 数据库地址
Host = 127.0.0.1
; 数据库名称
Name = v3
; 数据表前缀
TablePrefix = Cloudreve
```

保存后，进程序目录重新初始化，生成新的账号密码，之后再用 systemctl 管理，完事了

```bash
#进程序目录
cd /home/cloudreve
#启动程序
./cloudreve
```

另外提一嘴，程序默认监听端口也是在该文件修改。

## 更新

**V3 版本内升级步骤较为简单，总体流程如下：**

1. 备份数据库；

2. 下载或构建最新版本的 Cloudreve；

3. 停止正在运行的 Cloudreve；

4. 将老版本的 Cloudreve 主程序替换为新版本；

5. 启动 Cloudreve；

6. 清空浏览器缓存。

   > 如果你在老版本使用了自行构建的前端静态资源文件，请使用新版对应的前端仓库代码重新构建。

## 对接OneDrive

1、前往[Azure Active Directory 控制台 (国际版账号)](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 或者 [Azure Active Directory 控制台 (世纪互联账号)](https://portal.azure.cn/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)并登录，登录后进入`Azure Active Directory`管理面板。

2、进入左侧 `应用注册` 菜单，并点击 `新注册` 按钮。

3、填写应用注册表单。其中，名称可任取；`受支持的帐户类型` 选择为`任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户`；`重定向 URI (可选)`请选择`Web`，并填写`https://drive.codebaby.com.cn/api/v3/callback/onedrive/auth`； 其他保持默认即可

4、创建秘钥。

5、复制秘钥和ID到OneDrive存储策略页面，并填写相关资料，授权登陆即可。



## 最后

**更多安装方式和程序配置添加详见官方文档** （作者文档也咕了不少：

> https://docs.cloudreve.org/getting-started/config

## 附一个自己搭建的网盘
网址：https://drive.codebaby.com.cn/   
欢迎大家注册使用，**不限容量！！！**文件用OneDrive存储。【暂停使用！！！】

##  参考资料

https://www.feiyubk.com/archives/8.html
