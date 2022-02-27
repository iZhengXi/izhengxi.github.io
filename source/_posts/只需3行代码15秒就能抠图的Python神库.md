---
title: 只需3行代码15秒就能抠图的Python神库
tags:
  - Python
categories:
  - 编程笔记
  - Python
abbrlink: 382fae02
date: 2022-01-25 17:54:26
---

_这是一款网站：_[_Remove.bg_](https://www.remove.bg/)<br />_可以通过调用网站提供的API进行抠图，每月可以免费抠图50张。唯一缺点就是分辨率限制。_
<a name="toc-heading-1"></a>

<!-- more -->

## 实现步骤
**第一步：**<br />[网站上注册获取 API](https://www.remove.bg/api)<br />**第二步：**<br />安装抠图库<br />`pip install removebg`<br />**实现抠图**
```python
from removebg import RemoveBg
rmbg = RemoveBg("WPZ2Q4fraseKfAN9PPxxxxxx", "error.log") # 引号内是你获取的API
rmbg.remove_background_from_img_file("C:/Users/sony/Desktop/1.jpg") #图片地址
```
<a name="toc-heading-2"></a>
## 批量抠图
```python
from removebg import RemoveBg
import os
rmbg = RemoveBg("WPZ2Q4fraseKfAN9PPxxxxxx", "error.log")
path = '%s/picture'%os.getcwd() #图片放到程序的同级文件夹 picture 里面
for pic in os.listdir(path):
    rmbg.remove_background_from_img_file("%s\%s"%(path,pic))
```
GitHub 库地址：[https://github.com/brilam/remove-bg](https://github.com/brilam/remove-bg)
> 参考文献

1. [只需 3 行代码 5 秒就能抠图的 Python 神库](https://zhuanlan.zhihu.com/p/73073456)
1. [python利用Remove.bg接口自动去背景](https://blog.csdn.net/Quentin_he/article/details/97569625)
1. [Remove.bg](https://www.remove.bg/)
