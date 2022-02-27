---
title: Python中函数和方法的区别
tags:
  - Python
categories:
  - 编程笔记
  - Python
abbrlink: a2c3c0dd
date: 2022-01-09 15:32:26
---

_python中函数和方法是两个容易混淆的概念，他们其实有很多区别。_
<a name="toc-heading-1"></a>

<!-- more -->

## 1. 所处位置不同
函数是直接写文件中而不是class中，方法是只能写在class中。
<a name="toc-heading-2"></a>
## 2. 定义方式不同
函数定义的方式 def关键字 然后接函数名 再是括号 括号里面写形参也可以省略不写形参。<br />例如：
```python
def functionName():
    """这里是函数的注释"""
    print("这一块写函数的内容")
```
而方法的定义和函数有点区别，方法必须带一个默认参数，静态方法除外。
<a name="toc-heading-3"></a>
## 3. 调用的方式不同

1. 函数的调用：函数的调用是直接写 函数名(函数参数1,函数参数2,……)<br />
1. 方法的调用：方法是通过对象点方法调用的（这里是指对象方法）<br />

例如：
```python
class className:
    def method(self):
        print("这是一个方法")
#调用---------------------
#实例化对象
c=className()
c.method()
```
**总结：**

1. 函数是封装了一些独立的功能，可以直接调用，能将一些数据（参数）传递进去进行处理，然后返回一些数据（返回值），也可以没有返回值。可以直接在模块中进行定义使用。<br />所有传递给函数的数据都是显式传递的。<br />
1. 方法和函数类似，同样封装了独立的功能，但是方法是只能依靠类或者对象来调用的，表示针对性的操作。<br />方法中的数据self和cls是隐式传递的，即方法的调用者；<br />方法可以操作类内部的数据 **简单的说，函数在python中独立存在，可直接使用的，而方法是必须被别人调用才能实现的**。静态方法除外（与类和对象均无关，通过类名和对象名均可被调用，属函数）<br />

不过也要摒弃**错误认知**：并不是类中的调用都叫方法<br />例如：
```python
class Foo(object):
    def func(self):
        pass
#实例化
obj = Foo()
# 执行方式一:调用的func是方法
obj.func() #func 方法
# 执行方式二：调用的func是函数
Foo.func(123) # 函数
```
> 最大的区别是**参数传递**，方法是自动传参self，函数是主动传参

如果自己也不确定，可以打印类型查看：
```python
from types import FunctionType,MethodType
print(isinstance(obj.func,MethodType))    ---># True
print(isinstance(Foo.func,FunctionType))  ---># True
```
