# Python2综合笔记本
>本笔记本用来保存各种关于Python2不限应用范围的学习笔记。这些笔记包含很久以前就已经学会的知识，也包括不断学习中添加的。
>由于Python2已经确定于2020年停止更新，所以Python2笔记本维护次数会不断减少，同时增加Python3笔记维护次数。

<br>

###菜单：
 - [包导入](#包导入)
 - [闭包示例](#闭包示例)
 - [时间装饰器](#时间装饰器)
 - [字典使用法](#字典使用法)
 <br>
 
 

## 包导入
>包导入有基本4种形式：
> 1.使用方法1导入lib为基本导入方法，使用时需要使用完整的包路径
> 2.使用方法2是给lib导入时指定自定义库名，使用时需要使用完整的包路径

```python
import lib  # 1
import lib as libname # 2
```

> 3.从lib中导入模块，使用时不需要指定完整的包路径
> 4.从lib中导入模块，并自定义库名，使用时不需要指定完整的包路径

```python
from lib import module  # 3
from lib import module as libname  # 4
```
<br>

## 闭包示例
>Wiki: 在计算机科学中，闭包（Closure）是词法闭包（Lexical Closure）的简称，是引用了自由变量的函数。这个被引用的自由变量将和这个函数一同存在，即使已经离开了创造它的环境也不例外。所以，有另一种说法 认为闭包是由函数和与其相关的引用环境组合而成的实体。闭包在运行时可以有多个实例，不同的引用环境和相同的函数组合可以产生不同的实例。

```python
#def闭包
def outer(outer_numb):
	def inner(inner_numb):
		return outer_numb + inner_num
	return inner

#使用闭包
function = outer(10) # outer绑定数值:10
print function(20)   # => 30
```
<br>

## 时间装饰器
>时间装饰器主要用来计算一个函数完全执行所需要时长

---
```Python
import time # 时间库

def execTime(func):
	def timeFunc(*args , **kwargs):
		start_time = time.time()    # 开始时间戳
		back = func(*args,**kwargs) # 被装饰函数执行
		print "@ %.3fs : %s()" % (time.time() - start_time , func.__name__)
		return back
	return timeFunc
```
<br>

## 字典使用法
>字典有两种声明的方法，这两种方法都是有效的。

```python
dictionary = {} # dictionary = dict()
```
<br>

>字典插入值，如果值已经存在，改值会被更新，否则创建新的变量并初始化

```python
# dictionary = {'name': 'Tom', 'gender':'male'}

dictionary['name'] = 'Tom'
dictionary['gender'] = 'male'
```
<br>


>这两个方法，一个是获取该字典所有的key，另一个是获取该字典所有的value

```python
dictionary.keys()   # => ['name', 'gender']
dictionary.values() # => ['Tom', 'male']
print dictionary    # => {'gender': 'male', 'name': 'Tom'}
```
<br>

>通过get方法是安全获取字典的值，如果没有改变量，则默认显示`None`。当然可以自定义不存在提示值例如`NULL`

```python
dictionary.get('name')   # => Tom
dictionary.get('gender') # => male

dictionary.get('key')        # => None
dictionary.get('key','NULL') # => NULL
```
<br>

>安全检查字典是否有指定字典变量

```python
dictionary.has_key('name') # => True
dictionary.has_key('age')  # => False

‘name’ in dictionary # => True
'age' in dictionary  # => False

‘name’ not in dictionary # => False
'age' not in dictionary  # => True
```
<br>

>删除字典中的键值对

```python
del dictionary['name']
del dictionary['gender']
```
<br>

>弹出字典中的指定的键值对

```python
dictionary.pop('name')   # => Tom
dictionary.pop('gender') # => male
```
<br>

>  字典中使用方法汇总

```python
dict.keys()     # => 输出所有键 
dict.values()   # => 输出所有值

dict.items()    # => [('gender', 'male'), ('name', 'Tom')]
dict.clear()    # => 清空字典中的键值对
dict.copy()     # 返回字典的浅复制副本
                # 深复制: from copy import deepcopy
                # new_dict = deepcopy(dict)

dict.iteritems()   # 迭代器，返回键值对迭代器
dict.iterkeys()    # 迭代器，返回键迭代器
dict.itervalues()  # 迭代器，返回值迭代器

dict.has_key(key)  # => 检查字典中的
dict.pop(key,default=None)    # => 弹出键并返回值 
dict.fromkeys(seq, val=None)  # => 使用seq,val生成键值对
dict.get(key, default=None)   # => 安全获取键的结果
dict.setdefault(key, default=None) # => 插入值并带有缺省值
dict.update(x_dict)# => 使用x_dict更新dict字典的键值对
```

