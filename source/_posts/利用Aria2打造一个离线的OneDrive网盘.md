---
title: 利用Aria2打造一个离线的OneDrive网盘
date: 2021-01-13 15:32:26
tags:
- Aria2
- Rclone
categories:
- 网站建设
---

<a name="toc-heading-1"></a>
## 原理
Aria2 有一个配置项`on-download-complete`，在下载完后执行一个脚本。当下载完成后 Aria2 会给脚本传递3个变量 `$1`、`$2`、`$3` 分别为 gid 、文件数量、文件路径。利用这个配置项和这些变量就可以实现很多功能，比如下载完成后调用 Rclone 进行上传操作。整个过程简单来说就是，Aria2 下载文件到 VPS ，完成后告诉 Rclone 将这个文件上传到网盘。理论上只要是 Rclone 支持的网盘，都可以按照这个思路来实现~~伪~~离线下载。并且还可实现下载完成上传OneDrive后自动本地删除，不占用VPS空间。
<a name="toc-heading-2"></a>
## 安装 Aria2
这里使用 Aria2 一键安装管理脚本，执行下面的代码下载并运行脚本，出现脚本操作菜单输入 `1`开始安装。<br />`wget -N https://git.io/aria2.sh && chmod +x aria2.sh && bash aria2.sh`
<a name="toc-heading-3"></a>
## 安装和配置 Rclone
`curl https://rclone.org/install.sh | sudo bash`<br />安装完后，输入 `rclone config` 命令进入交互式配置选项，按照提示一步一步来进行操作即可。
<a name="toc-heading-4"></a>
### Rclone配置教程
**获取Token**<br />在本地 Windows 电脑上[下载 rclone](https://rclone.org/downloads/)，然后解压出来，解压后进入文件夹，在资源管理器地址栏输入`cmd`，回车就会在当前路径打开命令提示符。输入以下命令：<br />`rclone authorize "onedrive"`<br />接下来会弹出浏览器，要求你登录账号进行授权。授权完后命令提示符窗口会出现以下信息：
```bash
If your browser doesn't open automatically go to the following link: http://127.0.0.1:53682/auth
Log in and authorize rclone for access
Waiting for code...
Got code
Paste the following into your remote machine --->
{"access_token":"xxxxxxxx"}  # 注意!复制{xxxxxxxx}整个内容，并保存好，后面需要用到
<---End paste
```
配置Rclone<br />输入 `rclone config` 命令，会出现以下信息，参照下面的注释进行操作。
```bash
e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> n  # 选择n，新建
name> P3TERX   # 输入名称，类似于标签，用于区分不同的网盘。
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / A stackable unification remote, which can appear to merge the contents of several remotes
   \ "union"
 2 / Alias for a existing remote
   \ "alias"
 3 / Amazon Drive
   \ "amazon cloud drive"
 4 / Amazon S3 Compliant Storage Providers (AWS, Ceph, Dreamhost, IBM COS, Minio)
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
15 / Local Disk
   \ "local"
16 / Mega
   \ "mega"
17 / Microsoft Azure Blob Storage
   \ "azureblob"
18 / Microsoft OneDrive
   \ "onedrive"
19 / OpenDrive
   \ "opendrive"
20 / Openstack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
21 / Pcloud
   \ "pcloud"
22 / QingCloud Object Storage
   \ "qingstor"
23 / SSH/SFTP Connection
   \ "sftp"
24 / Webdav
   \ "webdav"
25 / Yandex Disk
   \ "yandex"
26 / http Connection
   \ "http"
Storage> 18  # 选择18，Microsoft OneDrive
** See help for onedrive backend at: https://rclone.org/onedrive/ **
Microsoft App Client Id
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_id>   # 留空，回车
Microsoft App Client Secret
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_secret>   # 留空，回车
Edit advanced config? (y/n)
y) Yes
n) No
y/n> n  # 选n
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes
n) No
y/n> n  # 选n
For this to work, you will need rclone available on a machine that has a web browser available.
Execute the following on your machine:
    rclone authorize "onedrive"
Then paste the result below:
result> {"XXXXXXXX"}  # 上面保存的token复制到这里
2018/10/31 19:54:06 ERROR : Failed to save new token in config file: section 'P3TERX' not found
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
Your choice> 1  # 这里问你要选择的类型，选1
Found 1 drives, please select the one you want to use:
0: OneDrive (business) 
Chose drive to use:> 0  # 程序找到网盘，这里编号是0，就选择0
Found drive 'root' of type 'business', URL: https://xxxxxx-my.sharepoint.com/personal/xxxxxxx/Documents
Is that okay?
y) Yes
n) No
y/n> y  # 选y
--------------------
[P3TERX]
type = onedrive
token = {"XXXXXXXX"}
drive_id = XXXXXXXXX
drive_type = business
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y  # 选y
Current remotes:
Name                 Type
====                 ====
P3TERX               onedrive
e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q  # 选q，退出
```
至此，Rclone 已成功连接到了 OneDrive 网盘。
<a name="toc-heading-5"></a>
## 配置自动上传脚本
Aria2 一键安装管理脚本整合了 Aria2 完美配置 ，安装时会下载自动上传脚本。考虑到不是所有人都需要上传，默认不启用，需要手动启用。<br />输入`nano /root/.aria2/autoupload.sh`打开自动上传脚本进行编辑，脚本中有中文注释，按照自己的实际情况进行修改，一般只需要修改下面2个部分。
```bash
name='Onedrive' #配置Rclone时的name
folder='/DRIVEX/Download' #网盘里的文件夹，留空为网盘根目录。
```
输入`nano /root/.aria2/aria2.conf`打开 Aria2 配置文件进行修改。或使用Aria2 一键安装管理脚本中的手动修改选项打开配置文件进行修改。找到“下载完成后执行的命令”，修改成下面的这样。
```
# 下载完成后执行的命令
# 删除.aria2文件
#on-download-complete=/root/.aria2/delete.aria2.sh
# 调用 rclone 上传(move)到网盘
on-download-complete=/root/.aria2/autoupload.sh
```
> - 在`on-download-complete=/root/.aria2/delete.aria2.sh`前加上`#`
> - 去掉`#on-download-complete=/root/.aria2/autoupload.sh`前面的`#`

**重启 Aria2**<br />`service aria2 restart`<br />**当你进行完以上所有操作，现在下载文件就会自动上传至相应的网盘。**
<a name="toc-heading-6"></a>
## 前端面板使用
现在，你可用通过命令行利用Aria2下载，不过比较繁琐，可以用一个前端面板进行远程连接下载。
<a name="toc-heading-7"></a>
### 安装 AriaNg
AriaNg是个 Web 前端，在项目的 releases页面下载后，上传至 VPS 进行部署，这里不做过多说明。~~作者直接提供了一个演示页面，是可以直接使用的。~~
> **Q：使用他人提供的页面会不会不安全？**
> A：设置是缓存在本地的，所填写的 RPC 地址和 RPC 密钥是不会上传的。如果不放心，可以使用本地程序或者自己部署。建议萌新学习一些建站经验后再尝试自己部署。

<a name="toc-heading-8"></a>
### 本地程序
[AriaNg Native](https://github.com/mayswind/AriaNg-Native/releases/tag/1.1.3) 是 Web 前端的本地化程序，比起网页端它功能会多一些，且不需要复杂的部署过程，下载安装后打开就可以使用，支持 Windows 和 macOS<br />另外，手机也有对应的Aria2客户端，例如[Aria2App](https://github.com/devgianlu/Aria2App)。
<a name="toc-heading-9"></a>
## 前后端连接
在`AriaNg 设置`中填写相关 RPC 信息。`RPC 地址`对应 IP 或域名， `RPC 秘钥`对应配置文件中`rpc-secret`选项后面的参数。如果没有过修改端口，就只需要填写`RPC 地址`和`RPC 密钥`两项。使用 Aria2 一键安装管理脚本安装后会显示这些信息，设置起来更简单。<br />![image](https://vip1.loli.io/2022/01/27/BAwglq9jT1dyohv.jpg)<br />
<br />在你没有完全了解 Aria2 的情况下，不建议去修改设置，保持默认即可。

- 在 AriaNg （或其它前端面板）中修改设置项，只有在运行中才有效，属于临时设置，它不会修改配置文件。重启或关闭 Aria2 后端程序后，会重新读取配置文件。所以必要的设置，需写入配置文件中。
- 如果在修改配置文件后，重启 Aria2 的过程中没有关闭 AriaNg ，AriaNg 可能会给服务端传递之前缓存的配置，这就导致修改的配置没有生效（理论上其它前端面板也是这样）
> 参考文献：

1. [Aria2 前端面板(GUI) AriaNg 使用教程](https://p3terx.com/archives/aria2-frontend-ariang-tutorial.html)
1. [Aria2 + Rclone 实现 OneDrive、Google Drive 等网盘离线下载](https://p3terx.com/archives/offline-download-of-onedrive-gdrive.html)
1. [Aria2 一键安装管理脚本](https://p3terx.com/archives/aria2-oneclick-installation-management-script.html)
1. [https://rclone.org/install/#script-installation](https://rclone.org/install/#script-installation)
1. [https://github.com/P3TERX/aria2_perfect_config](https://github.com/P3TERX/aria2_perfect_config)
1. [Aria2 官方手册](https://aria2.github.io/manual/en/html/aria2c.html)
1. [Rclone 官方手册](https://rclone.org/docs/)
