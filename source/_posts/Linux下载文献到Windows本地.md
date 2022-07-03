---
title: Linux下载文献到Windows本地
tags:
  - Linux
  - Windows
categories:
  - 生物信息学
abbrlink: 6e25baeb
date: 2022-06-25 10:32:06
---

**使用Xshell:**

```bash
使用xshell来操作服务非常方便，传文件也比较方便。
就是使用rz，sz
首先，服务器要安装了rz，sz
yum install lrzsz
当然你的本地windows主机也通过ssh连接了linux服务器
运行rz，会将windows的文件传到linux服务器
运行sz filename，会将文件下载到windows本地
```
<!-- more -->

**Ubuntu安装lrzsz:**
```bash
sudo apt-get update -y
sudo apt-get install -y lrzsz
```
