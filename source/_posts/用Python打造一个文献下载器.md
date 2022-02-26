---
title: 用Python打造一个文献下载器
date: 2022-01-23 15:22:26
tags:
- Python
categories:
- 编程笔记
- 	Python
---

文献下载有很多插件，例如[sci-hub button](https://greasyfork.org/zh-CN/scripts/370246-sci-hub-button)和[Scholarscope](https://www.scholarscope.cn/)，但是今天突然想用万能的Python实现一个自动下载文献的功能。
<a name="toc-heading-1"></a>

<!-- more -->

## 准备
<a name="toc-heading-2"></a>
### 安装Anaconda
安装完成后，打开`Jupyter Notebook`<br />![](https://vip1.loli.io/2022/01/25/hUSk4uLHjMycmrd.png)<br />**新建一个空白Python3文件**，保存为`pyhub.py`<br />输入：
```python
import webbrowser #打开默认浏览器的标准库
hub = "http://sci-hub.tw/"
print("Please enter DOI")
while(True):
    doi = input()
    paper = hub+doi
    webbrowser.open(paper)
```
点击运行后会自动打开默认浏览器，打开一个类似`http://sci-hub.tw/10.1038/s41586-019-1497-4`这样的链接，点击下载即可。
<a name="toc-heading-3"></a>
## 打包成exe
为了让程序运行更方便，我们可以利用python的[PyInstaller](http://www.pyinstaller.org/)库将程序打包成exe。<br />Python 默认并不包含 PyInstaller 模块，因此需要自行安装 PyInstaller 模块。
```python
pip install pyinstaller
```
PyInstaller **语法如下：**
```python
pyinstaller 选项 Python源文件
```
<a name="toc-heading-4"></a>
### PyInstaller 支持的常用选项
![](https://vip1.loli.io/2022/01/25/CrkYnTVylIOUeK1.png)<br />因此，我们可以在pyhub.py的目录下执行：
```python
Pyinstaller -F -w pyhub.py
```
执行完毕，便可以看我们生成的exe文件，点击即可执行。
