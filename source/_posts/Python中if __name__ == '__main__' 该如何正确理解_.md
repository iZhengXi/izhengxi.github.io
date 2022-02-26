---
title: Python中if __name__ == '__main__' 该如何正确理解?
date: 2022-01-25 14:32:26
tags:
- Python
categories:
- 编程笔记
-  	Python
---

一个python的文件有两种使用的方法，第一是直接作为脚本执行，第二是import到其他的python脚本中被调用（模块重用）执行。而`if __name__ == 'main':`的作用就是控制这两种情况执行代码的过程，存在`if __name__ == 'main':`时，该脚本可以直接运行，但是作为模块导入后则不会提前运行。<br />

<!-- more -->

例如test.py：

```python
#test.py
print('import the module')
def main():
    print('Hello, World!')
if __name__ == '__main__':
    main()
#end
```
直接运行该程序，会输出：
```python
>>>python test.py
import the module
Hello, World!
```
**同时输出了’import the module’和’Hello, World!’**<br />说明:`__name__ == '__main__'`是成立的，所以执行了下面的`main()`<br />**接下来请我们用import的方式**，在CMD中输入`python`，再输入`import test`
```python
>>>python
Python 3.6.5 |Anaconda, Inc.| (default, Mar 29 2018, 13:32:41) [MSC v.1900 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>>import test
import the module    #只输出了这个，没有Hello, World!
#这个时候
>>>test.__name__             
'test'
>>>__name__                     
'__main__'
```
只输出了’import the module’，没有输出’Hello, World!’<br />可以看出这个时候test模块的**name**=’test’<br />而当前程序的**name**=’**main**‘<br />无论怎样，test.py中的**name** == ‘**main**‘都不会成立的，也就意味着，当你是通过import的方法来执行这个.py文件时，不能运行if **name** == ‘**main**‘:下面的语句或者函数。<br />总结：<br />**name** 是当前模块名，当模块被直接运行时模块名为 **main** 。这句话的意思就是，当模块被直接运行时，以下代码块将被运行，当模块是被导入时，代码块不被运行。

