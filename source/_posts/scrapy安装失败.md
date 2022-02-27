---
title: scrapy安装失败
tags:
  - Python
  - scrapy
categories:
  - 编程笔记
  - Python
abbrlink: e1aca15a
date: 2022-01-25 17:59:26
---

<a name="toc-heading-1"></a>
## 问题描述
安装scrapy时提示：
```python
error: Microsoft Visual C++ 14.0 is required. Get it with "Microsoft Visual C++ Build Tools": http://landinghub.visualstudio.com/visual-cpp-build-tools
```
<a name="toc-heading-2"></a>

<!-- more -->

## 解决办法

<br />去该网站：[https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted](https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted) 下载对应的twisted的安装文件安装后即可。<br />例如：
```python
pip install "C:\Users\Howar Zheng\Downloads\Twisted-18.9.0-cp37-cp37m-win32.whl"
```

