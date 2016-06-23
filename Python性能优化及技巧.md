# Python性能优化及技巧
__作者: coreVK__
__版本: 0.0.1__
__环境: `Mac OS X 10.11.5`、`Python 2.7.10`__

##列表解析

列表解析要比循环构建一个List高效

```python
_list = ['a','b','c','d','e']
total = []

# 普通循环效率:1.64s
for i in range(1000000):
    for w in _list:
        total.append(w)
        
# 列表的效率:1.16s
for i in range(1000000):  
    a = [w for w in _list]  

# 元组的效率:6.36s(异常)
for i in range(1000000):  
    a = (w for w in _list) 
```


##字符串
使用join而不是‘+’

```python
_list = ['a','b','c','d','e','f','g','h','i']
string = ''

# 普通方法:0.344s
for i in xrange(100000):
    for sub in _list:
        string += sub
        
# 优化方法:0.274s
for i in xrange(100000):
    xlist = [ x for x in _list]
    string = ''.join(xlist)
```

##变量交换
交换两个变量,有性能优势

```python
#糟糕方法
tmp = a
b = a
b = tmp

#Python方法
a, b = b, a
```


