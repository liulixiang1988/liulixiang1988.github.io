Title: Python数据分析(1)-NumPy中的ndarray
Date: 2015-11-29 14:42
Category: Python
Tags: 数据分析
Author: 刘理想

[TOC]

`ndarray`是NumPy中的基本数据机构，别名`array`，利于节省内存和提高CPU计算时间，提供了丰富的函数。

##1. `ndarray`创建函数

函数|函数
--|--
arange|array
empty_like|empty
fromfile|fromfunction
identity|linespace
logspace|mgrid
ogrid|ones
ones_like|r
zeros|zeros_like

```
from numpy import *

aArray = array([1, 2, 3]) #一维数组
aArray
Out[9]: array([1, 2, 3])
bArray = array([(1, 2, 3), (4, 5, 6)]) #二维数组
bArray
Out[11]: 
array([[1, 2, 3],
       [4, 5, 6]])
zeros((2, 2)) #全0
Out[12]: 
array([[ 0.,  0.],
       [ 0.,  0.]])
arange(1, 5, 0.5) #间隔0.5的数组
Out[13]: array([ 1. ,  1.5,  2. ,  2.5,  3. ,  3.5,  4. ,  4.5])
```

##2. `ndarray`的基本运算符

```
>>> from numpy import *
>>> aArray = array([(5, 5, 5), (5, 5, 5)])
>>> bArray = array([(2, 2, 2), (2, 2, 2)])
>>> cArray = aArray*bArray
>>> cArray
array([[10, 10, 10],
       [10, 10, 10]])
>>> aArray += bArray
>>> aArray
array([[7, 7, 7],
       [7, 7, 7]])
>>> aArray > 5 #每个元素是否大于5
array([[ True,  True,  True],
       [ True,  True,  True]], dtype=bool)
```

##3. `ndarray`内建函数和标准函数

```
>>> aArray = array([(1, 2, 3), (4, 5, 6)])
>>> sin(aArray) #求array中的每个sin值
array([[ 0.84147098,  0.90929743,  0.14112001],
       [-0.7568025 , -0.95892427, -0.2794155 ]])
>>> aArray.shape #形状
(2, 3)
>>> bArray = aArray.reshape(3, 2) #更改形状
>>> bArray
array([[1, 2],
       [3, 4],
       [5, 6]])
>>> aArray.sum() #求全部的和
21
>>> aArray.sum(axis=0) #纵向求和
array([5, 7, 9])
>>> aArray.sum(axis=1) #横向求和
array([ 6, 15])
>>> cArray = array([1, 3, 5])
>>> cArray[:1] #切片
array([1])
>>> dArray = array([2, 4, 6])
>>> eArray = array([7, 8, 9])
>>> where(cArray > 2, dArray, eArray) #对每个元素运行相当于if else运算
array([7, 4, 6])
```

###3.1 `fromfunction`函数

`fromfunction`第一个参数是函数名，第二个参数是数字大小，序列是从0开始。下面打印一个九九乘法表：

```
In [9]: def fun(x, y):
   ...:     return (x+1)*(y+1)
   ...:

In [10]: arr = fromfunction(fun, (9, 9))

In [11]: arr
Out[11]:
array([[  1.,   2.,   3.,   4.,   5.,   6.,   7.,   8.,   9.],
       [  2.,   4.,   6.,   8.,  10.,  12.,  14.,  16.,  18.],
       [  3.,   6.,   9.,  12.,  15.,  18.,  21.,  24.,  27.],
       [  4.,   8.,  12.,  16.,  20.,  24.,  28.,  32.,  36.],
       [  5.,  10.,  15.,  20.,  25.,  30.,  35.,  40.,  45.],
       [  6.,  12.,  18.,  24.,  30.,  36.,  42.,  48.,  54.],
       [  7.,  14.,  21.,  28.,  35.,  42.,  49.,  56.,  63.],
       [  8.,  16.,  24.,  32.,  40.,  48.,  56.,  64.,  72.],
       [  9.,  18.,  27.,  36.,  45.,  54.,  63.,  72.,  81.]])
```

###3.2 `ufunc`函数

`ufunc`(universal function)是一种能对数组的每个元素进行操作的函数。NumPy内置的许多ufunc函数都是在C语言级别实现的，计算速度非常快。

可以通过`help(ufunc)`，`help(numpy)`来查看有哪些`ufunc`函数。

```
In [14]: a = np.arange(1, 5)

In [15]: a
Out[15]: array([1, 2, 3, 4])

In [16]: help(ufunc)


In [17]: b = np.arange(2, 6)

In [18]: b
Out[18]: array([2, 3, 4, 5])

In [19]: np.add(a, b)
Out[19]: array([3, 5, 7, 9])

In [20]: np.add.accumulate([2, 3, 8])
Out[20]: array([ 2,  5, 13])

In [21]: np.multiply.accumulate([2, 3, 8])
Out[21]: array([ 2,  6, 48])
```

作者：liulixiang1988#gmail.com (#换成@)
参考链接：https://www.coursera.org/learn/hipython/home/welcome