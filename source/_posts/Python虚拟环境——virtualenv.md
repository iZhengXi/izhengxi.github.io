---
title: Python虚拟环境——virtualenv
tags:
  - virtualenv
  - Python
categories:
  - 编程笔记
abbrlink: ed361030
date: 2020-09-13 08:42:26
---

_virtualenv是Python的一个工具包，可以创建完全隔离的Python环境，让一台电脑上同时拥有几个ython2和Python3的环境。_
<a name="toc-heading-1"></a>

<!-- more -->

## 基本使用
**安装**
```python
pip install virtualenv
mkdir my_project_dir
cd my_project_dir
virtualenv venv #venv为虚拟环境的目录名，目录名可以自定义
```
**不过，也定义自定义python解释器**
```python
virtualenv -p /usr/bin/python3.7 venv # -p参数用来指定Python解释器
```

<br />**激活环境**
```python
source venv/bin/activate
```
从现在起，利用pip安装的任何包都会放在venv文件夹中。<br />**暂停环境**
```python
. venv/bin/deactivate
```
**删除环境**<br />因为该虚拟环境是完全隔离的，因此删除该虚拟环境对应的文件夹就行了。
<a name="toc-heading-2"></a>
## virtualenvwrapper介绍
鉴于virtualenv不便于对虚拟环境集中管理，所以推荐直接使用virtualenvwrapper。 virtualenvwrapper提供了一系列命令使得和虚拟环境工作变得便利。它把你所有的虚拟环境都放在一个地方。<br />**安装virtualenvwrapper(确保virtualenv已安装)**
```python
pip install virtualenvwrapper
pip install virtualenvwrapper-win　　#Windows使用该命令
```
**安装完成后，在~/.bashrc写入以下内容**
```python
export WORKON_HOME=~/Envs
source /usr/local/bin/virtualenvwrapper.sh
```
第一行：virtualenvwrapper存放虚拟环境目录<br />第二行：virtrualenvwrapper会安装到python的bin目录下，所以该路径是python安装目录下bin/virtualenvwrapper.sh
```python
source ~/.bashrc　　　　#读入配置文件，立即生效
```
<a name="toc-heading-3"></a>
## virtualenvwrapper基本使用
<a name="toc-heading-4"></a>
### 1.创建虚拟环境　mkvirtualenv
```python
mkvirtualenv venv
```
这样会在WORKON_HOME变量指定的目录下新建名为venv的虚拟环境。<br />若想指定python版本，可通过”–python”指定python解释器
```python
mkvirtualenv --python=/usr/local/python3.5.3/bin/python venv
```
<a name="toc-heading-5"></a>
### 2. 基本命令
**查看当前的虚拟环境目录**
```python
[root@localhost ~]# workon
py2
py3
```
**切换到虚拟环境**
```python
[root@localhost ~]# workon py3
(py3) [root@localhost ~]#
```
**退出虚拟环境**
```python
(py3) [root@localhost ~]# deactivate
[root@localhost ~]#
```
**删除虚拟环境**
```python
rmvirtualenv venv
```

---

**参考文献**<br />[https://www.cnblogs.com/technologylife/p/6635631.html](https://www.cnblogs.com/technologylife/p/6635631.html)<br />[https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000)
