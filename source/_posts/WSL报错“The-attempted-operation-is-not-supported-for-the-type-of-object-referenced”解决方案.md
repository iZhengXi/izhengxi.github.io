---
title: WSL报错“The attempted operation is not supported for the type of object referenced”解决方案
tags:
  - WSL
  - Linux
  - NoLsp
categories:
  - 生物信息学
date: 2022-06-030 10:32:06
---


最近在使用WSL2 ubuntu 22.04 LTS时突然出现如下报错：

<!-- more -->

![](https://static.gridea.dev/335395751264780883/FfSvWM4MV.png)

经过查询资料以后，造成该报错的原因是代理服务器和WSL之间的网络端口存在冲突，目前有两种解决方案。

第1种：

```bash
netsh winsock reset # 管理员权限运行cmd，运行该命令。
```

这种方法的缺点是，每次重启WSL后，可能需要重复执行该命令。

第2种：

下载[NoLsp.exe](http://www.proxifier.com/tmp/Test20200228/NoLsp.exe)

```bash
NoLsp.exe C:\Windows\system32\wsl.exe # 管理员权限打开cmd，在NoLsp.exe目录下执行该命令
```





**参考资料：**
1.[The Solution to WSL Error “The attempted operation is not supported for the type of object referenced”](https://wangyj.medium.com/the-solution-to-wsl-error-the-attempted-operation-is-not-supported-for-the-type-of-object-aa559854d1e3)

2.[使用nolsp.exe 解决wsl、docker desktop无法启动问题_licheng_god的博客-CSDN博客](https://blog.csdn.net/qq_28421553/article/details/119915116)

