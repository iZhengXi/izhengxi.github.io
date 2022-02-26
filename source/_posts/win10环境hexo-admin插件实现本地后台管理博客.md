---
title: win10环境hexo-admin插件实现本地后台管理博客
date: 2022-01-25 18:02:26
tags:
- Hexo
categories:
- 网站建设
- 	Hexo
---

Hexo每次发布和部署文章都需要在命令行下操作，比较繁琐，`hexo-admin`这个插件可以给你的Hexo博客搭建一个后台。
<a name="toc-heading-1"></a>

<!-- more -->

## 安装插件
```
npm install --save hexo-admin
hexo server -d
open http://localhost:4000/admin
```
之后，在浏览器里打开[http://localhost:4000/admin即可访问后台。](http://localhost:4000/admin%E5%8D%B3%E5%8F%AF%E8%AE%BF%E9%97%AE%E5%90%8E%E5%8F%B0%E3%80%82)<br />后台界面：<br />![](https://vip2.loli.io/2022/01/25/IuSb4sDmdRyCn7P.png)<br />可以直接新建文章，新建页面，写完文章后还能直接部署。<br />部署之前需要先进行配置：<br />在站点根目录新建文件`hexo-generate.bat`，在文件中写入`hexo g -d`。<br />之后，打开根目录站点配置文件：<br />**新增**
```yaml
admin:
    deployCommand: 'hexo-generate.bat'
```
现在，后台写完文章可以直接部署了。<br />为了更好的随时随地写文章，可以设置win10开机自启`hexo serve`<br />需要创建2个脚本，一个为vbs脚本，一个为bat脚本。<br />vbs脚本放到启动文件夹，用于运行bat脚本，而bat脚本用于启动hexo server<br />**创建vbs脚本**<br />`set ws=WScript.CreateObject("WScript.Shell")`<br />`ws.Run "E:\\WorkSpace\\webProject\\Hexo-blog\\hexo-server.bat /start",0`<br />**创建bat脚本**<br />`E:`<br />`cd E:/WorkSpace/webProject/Hexo-Blog`<br />`hexo s -d`<br />将**vbs脚本**放到启动文件夹<br />win10 启动文件夹目录为<br />`C:\Users\你的用户名\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup`<br />这样就能实现开机启动`hexo server`了，剩下的一切都可以交给浏览器和`hexo-admin`了。如果使用七牛，则可以使用`hexo-admin with qiniu`。
> **后台还可以设置密码访问。**

![](https://vip2.loli.io/2022/01/25/SAUP5W3pwhTkycm.png)<br />后台设置**用户名**和**密码**后，将它复制到**根目录站点配置**文件即可。
> 参考文献：

1. [https://blog.csdn.net/upc_xbt/article/details/54020135](https://blog.csdn.net/upc_xbt/article/details/54020135)
1. [https://segmentfault.com/a/1190000018488921](https://segmentfault.com/a/1190000018488921)



