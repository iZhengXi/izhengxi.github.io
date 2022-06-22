---
title: Office 365 E5续订策略
tags:
  - Office
  - E5
categories:
  - 网络资源
date: 2022-03-12 14:32:26
---

# 一、qyi.io

## 1、注册自己的api key

登录进入 [azure](https://portal.azure.com/#home) ，登录账号使用你的e5账户 ，就是以xxx.onmicrosoft.com开头的的账户。
搜索“应用注册”

<!-- more -->

2021-03-15：

**现在不能直接搜索到了**，目前两种方法可以找到 应用注册

### 1）请搜索 “Azure Active Directory”，然后在 管理-应用注册 ，

![img](https://vip2.loli.io/2022/03/12/SgfX6aWQYAG9RKp.png)

### 2）或者直接点击直达链接：

https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps

点击 新注册
![img](https://vip1.loli.io/2022/03/12/lfan3jwL1sMbSKU.png)
名称随意取一个，但是**最重要的是 “重定向 URI (可选)”**，请填写为下列地址，不然程序收不到回调。

```
https://e5.qyi.io/outlook/auth2/receive
```

2020-03-02 15:32
小伙伴们注意了，这里**受支持的账户类型 重定向 URI (可选)**一定要填对，不然无法授权的哦。
![img](https://vip1.loli.io/2022/03/12/W3f4D7xwHgtVPuC.png)

### 3）点击注册后记录以下信息:

**1、应用程序(客户端)ID**
**2、客户端密码**

应用程序(客户端)ID:
![img](https://vip2.loli.io/2022/03/12/PK8dlgIx9O1MCaX.png)
创建客户端密码：
![img](https://vip1.loli.io/2022/03/12/dEGVf5YIpOQUN2x.png)

**2021-03-07 ：**

**最近很多同学反应授权报错 Invalid client secret is provided. 这里请注意了，客户端密码请复制 “值”，不要复制 ID。**

## 2021-08-19：

**客户端密码这里已经没有了“从不”，只能选2年。**(别在意这些，谁也不知道2年后微软的策略会不会变，2年换一次密钥也不麻烦)

 

![img](https://vip1.loli.io/2022/03/12/a9mFMk8isyEnj2Y.png)
保存好以上2个key，一会需要用到。
配置api权限
![img](https://vip1.loli.io/2022/03/12/m7UwdunpCSFQePN.png)
![img](https://vip2.loli.io/2022/03/12/HGe89EY1biptXqJ.png)
勾选一下四个选项后，同时点击 **代表XX授予管理员同意**
PS: 这一步如果用的是子账号创建的api，那么这一项是灰色的，不能点击。需要登录 管理员 账号，再点击 **代表XX授予管理员同意**
![img](https://vip1.loli.io/2022/03/12/9Xmu2xUSCk3csy5.png)
这个时候api的配置就算完成了

## 2、添加key到自动订阅程序

进入 https://e5.qyi.io/
这里需要github账户登录，同样的，我只能获取你在 github中的 用户id、用户名等基础信息(邮箱获取不了)，其他的任何信息也获取不到(可自行尝试注册一个github Apps测试)。
![img](https://vip1.loli.io/2022/03/12/GfHo5uNQ2RBlaU6.png)

### 点击

**图标**

登录后进入主页面

### 点击 **新建** 

名称随意输入，只是个标识而已
描述可空
![img](https://vip2.loli.io/2022/03/12/yNC14mP6tnBkfFO.png)

### 点击 配置

![img](https://vip1.loli.io/2022/03/12/ro1atYnOFmJRKsU.png)
填入上一步记录的 **应用程序(客户端)ID、客户端密码** 
client_id ->**应用程序(客户端)ID**
client_secret->**客户端密码** 
![img](https://vip1.loli.io/2022/03/12/YI67W2DmHeglCs5.png)
点击下一步进行配置调用时间，
![img](https://vip2.loli.io/2022/03/12/n4CKdHrDPLTv8eu.png)

- 说明：单位 秒（最低调用频率为 60 秒,最高为6小时），例如: 30-60,代表在30秒-60秒之间随机调用一次

再点击下一步，到了 **授权 ，**
![img](https://vip1.loli.io/2022/03/12/uSBVIqGs9TfQakt.png)
此时会跳转到 microsoftonline Auth2.0授权页面，在这里请注意使用同一个域下的空账号(子账号)进行授权。
(因为在这一步我能获取到授权的outlook账户 邮件，但是程序不会保存，仅仅是调用api。)以免在以后发生误会。
![img](https://vip2.loli.io/2022/03/12/Vzype3WF1PThuJ4.png)
点击 接受 后，将会跳转回自动订阅程序。
![img](https://vip1.loli.io/2022/03/12/JjyuKnTAvidkzLF.png)
到此时，授权就完成了。

## 3、下一步

已经没啦~
到这里你就可以不用管了，程序会每两个小时调用一次outlook的api。
界面写得辣鸡，因为我不会前端呀~大家将就看看就行了。
过几天我会把删除功能加上，可以删除在程序里注册的账户。
交流群：959720211

### 还是要说一下隐私安全问题

因为有几个读者也说到了，统一回答下

1. api权限仅拥有 openid offline_access Mail.Read Mail.ReadWrite Mail.ReadBasic Mail.ReadBasic.ALL 这6个权限
2. 也就是说我仅仅能读取授权账户的邮件，其他任何事都做不了，且我写的这个程序没有保存任何除key之外的信息
3. 所以一开始我就说了，用子账户进行授权（空账户）及创建api，这样不涉及到 隐私及安全问题。
4. 如果实在担心不想用了，[azure](https://portal.azure.com/#home) 直接删掉api就可以了。

# 二、Telegram Bot

office e5开发者订阅每三个月续期一次，前提是活跃的调用api，所以有大神开发出telegram bot来实现自动刷新api。

项目官方地址:https://github.com/iyear/E5SubBot

**以下文档及图片均来自项目作者，本文仅稍作修改**

使用方法:
1.telegram绑定机器人
项目作者的bot:https://t.me/E5Sub_bot
本博客作者的bot:https://t.me/e5_paddi_bot
推荐使用第一个bot

2.进入https://azure.microsoft.com/zh-cn

2.右上角登录

3.点击右上角“门户”

4.点击中间“管理 Azure Active Directory”下的“查看”
[![image.png](https://vip1.loli.io/2022/03/12/lvVkY3jJFfLEsC6.png)

5.点击左侧箭头，在扩展栏里选择“应用注册”
![image.png](https://vip2.loli.io/2022/03/12/PauzMVelRZK48Ft.png)

6.点击“新注册”
![image.png](https://vip2.loli.io/2022/03/12/Sy6JcZPBVYeEadI.png)

7.名称随便填，选择**任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户和个人 Microsoft 帐户(例如，Skype、
Xbox)**，重定向 URI 类型选 Web 输入框填http://localhost/e5sub
![image.png](https://vip1.loli.io/2022/03/12/HDZJ3relbfX5vgt.png)



8.记好应用程序(客户端) ID（所谓的 client_id）
![image.png](https://vip2.loli.io/2022/03/12/gThatOvYL8fBw5r.png)





9.点击侧边栏的“证书和密码”，选择“新客户端密码”
![image.png](https://vip2.loli.io/2022/03/12/hqXePOZmwDHV7pa.png)



10.说明随便填，截止期限自己选择，点“完成”
![image.png](https://vip1.loli.io/2022/03/12/bVFrH9CtwudeTjL.png)



11.保存好客户端密码！！！保存好客户端密码！！！保存好客户端密码！！！只显示一
遍！！！（客户端密码就是 client_secret）
![image.png](https://vip2.loli.io/2022/03/12/McUKts9AjOio8rL.png)



12.点击侧栏“API 权限”，点击“添加权限”
![image.png](https://vip1.loli.io/2022/03/12/vTEMzIKG7Jc4Zxa.png)

13.在右侧选“Microsoft Graph”
![image.png](https://vip2.loli.io/2022/03/12/9aBpnXowUeQIGqc.png)



14.点击“委托的权限”并在下面选择“openid、offline_access、mail.read”并添加
![image.png](https://vip2.loli.io/2022/03/12/h6wnQjH41saMU7r.png)



15.点击“代表 xxx 授予管理员同意”
![image.png](https://img30.360buyimg.com/pop/jfs/t1/96240/18/20638/127332/61d1b408Eee6b783d/ccda3af2147eac02.png)

16.点击“是”并等待（不行就重复步骤 15 和 16 并刷新）
![image.png](https://vip1.loli.io/2022/03/12/PKoz4fsChWi5wpe.png)





17.然后我们回到我们绑定的telegram bot里，向bot发送/bind来绑定账户，注意，**不要点击应用注册: 点击直达**
![image.png](https://vip1.loli.io/2022/03/12/rH6t3YNvsUlZ7ik.png)

18.回复 client_id+空格+client_secret（在步骤 8 和步骤 11 中取得id和secret中间一定要空格隔开）
![image.png](https://vip2.loli.io/2022/03/12/ngPwM16ZrC4zkBt.png)



19.打开链接，登录你的e5账号（建议用子账号，管理员也可以），然后等待浏览器跳转

20.复制浏览器地址栏里的网址一定是**[http://localhost](http://localhost/)**开头的，如:

```
http://localhost/e5sub?code=OAQABAAIAAAAGV_bv21oQQ4ROqh0_1-tAObGXtDgPSww8tJb4emamOwwCKkccit_Hs
H5WCj37CGLJvN7IXlyXO8piHqtW1Yyg5aB_iVV9b1INX1WvaS1zDlXsKrDwq1f4vjPN2bulUarT0OmHh47ROl4JCUkRB8b
G0dwJSP7FJDPWXXVSfoCFKM9oLNuDi-V6q3inpEz5FIGrYzn0RI34ka_Jp0j-bBCUZo_4Judw77Jzg9CssHnsjY7HBIYIr
cZ-hHnNyWWzZ1-FERIl8A6AKWf8L_ch7B1f4nJTBtEvBEfAdcXMIN6dHyE0pNuJsAudQHkF0_Cr_Y-siNGndtAbMZm_vtB
4cNyLUQQAoTDXxDP44l_NUsEq0B1_BRqQMRKObywUAqzZL70N2TB_p4mLGn_V3RkE1POFYNoMSU-CEgRR5Auoz1aQlEcTe
RsCOwV02KLYDJoyy1hEq6UIHnkb3EebND3fvdYV2FQ6R0LwULBPN7VotTXi2vG2KGEAA-O4foiIlDEOoOQGO7uke1L-owh
BHiIHiyv4VfEnCoEvxAQrlEN-NvXcjKD6xG2z8BJGvsDY8O11VU4V1zVcEQFiu4mJxHKfsgwHVJ2SiMoLvATeOigsRCHF8
Vl1cQeBDM8EsROLFoBY_78gAA&session_state=a422855f-af51-4578-9ecf-76da5caa6175）
```

21.将上面复制的**链接+空格+你想备注的名称**发给bot(一定要按格式回复，否则会显示wrong format)
![image.png](https://vip2.loli.io/2022/03/12/FhqsR79BDxAOcGL.png)



22.到此完成，随后bot会定时刷新api，刷新成功会发消息给你

# 三、Microsoft 365 E5 Renew X

详见：https://blog.csdn.net/qq_33212020/article/details/119747634



# 四、herokuapp + OneManager

详见：https://github.com/qkqpttgf/OneManager-php

# 五、Rclone

Rclone 真的是个很棒的东西，它相当于将网盘挂载到本地，你可以在本地进行上传和下载而不用再去官网进行下载，但是有人就会说，用本地的 Win10 自带的 Onedrive 不就好了嘛，确实，但是用不了无法解决我能咋办嘛，还得跑去网页端上传，还得忍受微软上传的卡顿[<img src="https://vip2.loli.io/2022/03/12/ClMrI8ZnLyAB34b.jpg" alt="Office 365 E5 如何续期" style="zoom:67%;" />

而且因为是第三方挂载进行上传和下载，这个也是调用到了 API ，所以我在那天上传了 8G 的图片然后再删除，第二天就续费成功了，初步判断可能是 Rclone 可以续期，但也不一定，接下来说操作方法

Rclone 下载地址：[Github](https://github.com/rclone/rclone)，[官网](https://rclone.org/downloads/)

下载完成后解压到文件夹，然后在文件夹里面调用 CMD 或者 Git Bash ，然后进行配置

```bash
E:\rclone
$ rclone config
Current remotes:

Name                 Type
====                 ====

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> n   #输入n，创建新的远程连接
name> OneDrive   #给创建的远程连接，取个名字
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / A stackable unification remote, which can appear to merge the contents of several remotes
   \ "union"
 2 / Alias for an existing remote
   \ "alias"
 3 / Amazon Drive
   \ "amazon cloud drive"
 4 / Amazon S3 Compliant Storage Provider (AWS, Alibaba, Ceph, Digital Ocean, Dreamhost, IBM COS, Minio, etc)
   \ "s3"
 5 / Backblaze B2
   \ "b2"
 6 / Box
   \ "box"
 7 / Cache a remote
   \ "cache"
 8 / Dropbox
   \ "dropbox"
 9 / Encrypt/Decrypt a remote
   \ "crypt"
10 / FTP Connection
   \ "ftp"
11 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
12 / Google Drive
   \ "drive"
13 / Hubic
   \ "hubic"
14 / JottaCloud
   \ "jottacloud"
15 / Koofr
   \ "koofr"
16 / Local Disk
   \ "local"
17 / Mega
   \ "mega"
18 / Microsoft Azure Blob Storage
   \ "azureblob"
19 / Microsoft OneDrive
   \ "onedrive"
20 / OpenDrive
   \ "opendrive"
21 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
22 / Pcloud
   \ "pcloud"
23 / QingCloud Object Storage
   \ "qingstor"
24 / SSH/SFTP Connection
   \ "sftp"
25 / Webdav
   \ "webdav"
26 / Yandex Disk
   \ "yandex"
27 / http Connection
   \ "http"
Storage> 19   #选择上面所列出来的网盘名称，Microsoft OneDrive 是19，我们这里就输入19，实际操作可能跟我这里不一样，请自行查找 Onedrive
** See help for onedrive backend at: https://rclone.org/onedrive/ **

Microsoft App Client Id
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_id>    #留空，你也可以自己去申请好 API 密钥机密之类的，然后填入
Microsoft App Client Secret
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_secret>    #留空，你也可以自己去申请好 API 密钥机密之类的，然后填入
Edit advanced config? (y/n)
y) Yes
n) No
y/n> n   #不设置高级配置，选择n
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes
n) No
y/n> y   #选择y，自动配置授权
If your browser doesnt open automatically go to the following link: http://127.0.0.1:53682/auth
Log in and authorize rclone for access
Waiting for code...
Got code
Choose a number from below, or type in an existing value
 1 / OneDrive Personal or Business
 \ "onedrive"
 2 / Root Sharepoint site
 \ "sharepoint"
 3 / Type in driveID
 \ "driveid"
 4 / Type in SiteID
 \ "siteid"
 5 / Search a Sharepoint site
 \ "search"
Your choice>1   #输入1 选择 OneDrive Personal or Business 类型的网盘
Found 1 drives, please select the one you want to use:
0: OneDrive (business) id=b!qDQvcsZUTU-8eoYyKmtyyP1Jc0D8urZLlkATnfH1nWdJ1kkbrLsvQZLzVUTpeTrc
Chose drive to use:> 0              #输入0
Found drive 'root' of type 'business', URL: https://pmjs-my.sharepoint.com/personal/wld_365_w/Documents
Is that okay?
y) Yes
n) No
y/n> y    #选择y
--------------------
[OneDrive]
type = onedrive
token = {"access_token":"EwB4A8l6BAAURSN/FHlDW5xN74t6GzbtsBBeBUYAAZr+nsbvDJvpTZnIGTclACszh9PmiR6klQruRt9oBU5AD5ReAZLULrKBbFjgQzmUJHTW1Qg9EI2zCoj+/XMlp4M0V2sraXxwnDZvP/xHtLgMGIF3PLOjlSU0ThhHyB5OjIIeh3KCdA4/RIkAALoI7x5ycwXQLuBJ+D/iX3QwJFhVO4or7ogiaVUF0I3oF/A7dOEBJljUwHnBhYeyjOEpCRtoOXIrKl08afJbKtjVDXricLu4aXAIfBYibI7wffxQNxC2AWb5Z6TQ6BQUpUVs2Q48MUYCsDRshbyNhWxZOVlhtjOr3jRGVfDBb6iPuglwVaozSF68RRQbxc+L3QZ7aC4DZgAACLr0PZo0+g2xSAJr0YPcN9jCXJJvW9rHx9mt39W0nLlOUDzHTgi9mNNeAQmxfhFwlMxOr23MMW+Ux2fv7lg1uVdPdMSIPsRlNSD66FN/YwFHJ58NVZif+2CO38vFMgA5OCR0xV/AZ3OP4qhLt6eCGR4/AZ2L99UltF7pckGXOjDHaHSNIGP6gkgdlyurEdsLZa/KhApGapSGNyKhhR9Eiwhdbnfksn5flspsFkjWEbu4IJ7I8v7SpNXvTcIErhc4fIR3Kdk+55owCbtpGjzU435RmTZDs0LqU09DLAobhPXAB0MiansnU5vsrlLvudbYm5n9To549gTkPfwCBCjPkr+Xlk8jeJ2prlDyksaXlX0EdukBbA+x0FBaEHmaxExK7w19DjmXXj8MCs1RF3dawbegyLwSnq2+V9M/sqUxVO4uHG+Pw1Bds7L+ysAM+Tcu9BBb7t8/XEpaHzYF5XO8Q9pCOhhcUO8fsI8aA1aupBSbVf0W/AyKsrasUTZiYLgFsz6lYDgF0t5XyD/YGmTHwgutPW32HfjlQ4Nd8g+be+Lllmyyywve28Ynyy7ZitKJkQ4OKRWcBwYyOikJNvG/RZYUXEy6XJtyDJqyaEwE7PVriEPGzPtmT49hixjww8yjyZdoamUbM42/UTMPxIKTFqpOtMKnPZp5SJUpQwMgkzQU6hgqo6gPRrbC8XSR5qquRdvcSgfDTs+FaUgssCsxExPzUKqGFUGtCc7YYitI9Wh16MjhteeUfLVOruJapGcSRgOOXmwE76qf+bSfN80jrZ+mIH0C","token_type":"Bearer","refresh_token":"MCdzaJTxbvjx0gqccalX3SwZ84Q1D*Hoc2plXP2lNLLyYXeseIDzIzXF9GSKcl35vabYNK1PlNbHkn7zJULI6TEHJkvsMj3vMGG9xODHnZhhoD6r74yYxBC9G72RhIwo2n*qA!rvP7yhGShwH8RC1DxmMhUtmfBO2kpXvkAONhNG8nN9zWaGsXLXh0QDpCIu2!rLrpqH0Bi0amMocXotNGEFHrASLP583x2fpX5Da3VY*AFM18uIpsvg7i0aTj*RBZMvIV7OD02e1fAplBUmMzDodYGVelOFBKo8gd*xqZYYe7eQWaAv1SYuMBCduMcgewV71IUFw5TsdYONN5y!ewQcrv9*hhxoLI8xK7VY8VSwG8!9e21N6yLYFFHsTYZMMUOcF0LFEJmo1b29xRkShfuXXk6mxajTCn3IUbOKKP9EW","expiry":"2018-12-14T19:12:44.8939629+08:00"}
#这段方括号里的自己复制好，很重要
drive_id = 28acee48ba0a80c9
drive_type = personal
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y   #选择y
Current remotes:

Name                 Type
====                 ====
OneDrive             onedrive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q>
```

Rclone config 地址![Office 365 E5 如何续期](https://vip2.loli.io/2022/03/12/ejxrHPgU9uZSVYD.png)

rclone 到此就配置结束了，然后你可以挂载到本地磁盘，用啥软件之类的，rclone 原生好像是支持挂载的，但好像要一直开着 CMD 所以我直接介绍另外以一种 Rclone Browser

去 [Github](https://github.com/kapitainsky/RcloneBrowser/releases) 下载 Rclone Browser![Office 365 E5 如何续期](https://vip2.loli.io/2022/03/12/xbTGeEF3V7BgpnJ.png)

下载好后进行安装，然后进行配置，配置 rclone.exe 的路径还有 rclone.conf 配置文件的路径![Office 365 E5 如何续期](https://vip2.loli.io/2022/03/12/E4UxZqMhruJO1Pl.png)

配置好就可以看见网盘了，点进去就有内容了，然后使用 Upload 和 Download 就可以上传下载了

# 参考资料

1、https://qyi.io/archives/687.html

2、[使用telegram bot定期刷新office e5订阅api实现免费续期](https://blog.paddi.cc/index.php/archives/13/)

3、[Office 365 E5 如何续期](https://tdeh.top/archives/technology/office-365-e5-renew/)

4、[Office 365 E5 账号申请及永久续期教程](https://logi.im/script/permanently-keeping-an-office-e5-account.html)
