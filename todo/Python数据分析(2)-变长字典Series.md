Title: Python数据分析(2)-变长字典Series
Date: 2015-11-29 15:11
Category: Python
Tags: 数据分析
Author: 刘理想

[TOC]

我们知道字典是一种无序的数据结构，而pandas中的`Series`的数据结构不一样，它相当于定长有序的字典，并且它的index和value之间是独立的。

##1. `Series`的创建

`Series`的基本特征

- 类似一维数组的对象
- 由数据和索引组成

`Series`的创建：

```python
In [1]: import pandas as pd
In [2]: aSer = pd.Series([1, 2.0, 'a'])
In [3]: aSer
Out[3]:
0    1
1    2
2    a
dtype: object
```

我们可以看到`Series`自带索引，当然我们还可以指定索引：

```python
In [4]: bSer = pd.Series(['apple', 'peach', 'lemon'], index=[1, 2, 3])
In [5]: bSer
Out[5]:
1    apple
2    peach
3    lemon
dtype: object
In [7]: bSer.index #查看index
Out[7]: Int64Index([1, 2, 3], dtype='int64')
In [8]: bSer.values #查看values
Out[8]: array(['apple', 'peach', 'lemon'], dtype=object)
```

##2. `Series`的基本运算

`Series`与字典类似，可以通过`index`来访问数据。

```python
In [10]: aSer = pd.Series([3, 5, 7], index=['a', 'b', 'c'])

In [11]: aSer['b']
Out[11]: 5

In [12]: aSer*2 #对基本元素进行运算
Out[12]:
a     6
b    10
c    14
dtype: int64

In [13]: import numpy as np

In [14]: np.exp(aSer) #计算自然对数的n次方
Out[14]:
a      20.085537
b     148.413159
c    1096.633158
dtype: float64
```

##3. `Series`的数据对齐

数据对齐是很多数据处理软件里面里都有的一个功能，比如Excel。就是有一个表，前面是索引，后面是数据，可能索引没有对应的数据，也就是那一行为空的，只有一个索引值

```python
In [15]: data = {'AXP':'86.40', 'CSCO':'122.64', 'BA':'99.44'}
In [16]: sindex=['AXP', 'CSCO', 'BA', 'AAPL']
In [17]: aSer = pd.Series(data, index=sindex) #注意这里是用字典来定义的，跟前面的list定义不一样
In [18]: aSer
Out[18]:
AXP      86.40
CSCO    122.64
BA       99.44
AAPL       NaN
dtype: object

In [19]: pd.isnull(aSer) #检测哪些值是空的
Out[19]:
AXP     False
CSCO    False
BA      False
AAPL     True
dtype: bool
```

在算术运算中，可以自动对齐不同索引的数据，注意，只要其中一个`Series`不存在对应的`index`，对应的结果就会为`NaN`：

```python
In [20]: aSer = pd.Series(data, index=sindex)
In [21]: aSer
Out[21]:
AXP      86.40
CSCO    122.64
BA       99.44
AAPL       NaN
dtype: object

In [22]: bSer = {'AXP': '86.40', 'CSCO':'122.64', 'CVX':'23.78'}
In [23]: cSer = pd.Series(bSer)
In [24]: aSer + cSer
Out[24]:
AAPL             NaN
AXP       86.4086.40
BA               NaN
CSCO    122.64122.64
CVX              NaN
dtype: object
```

##4. `Series`的`name`属性

`Series`对象本身及其索引均有一个`name`属性，并且`Series`的`name`属性与其他重要功能有密切关系。

```python
In [27]: aSer = pd.Series(data, index=sindex)
In [28]: aSer.name = 'cnames' #指定对象的name属性
In [29]: aSer.index.name = 'valume' #指定索引本身的name属性
In [30]: aSer
Out[30]:
valume
AXP      86.40
CSCO    122.64
BA       99.44
AAPL       NaN
Name: cnames, dtype: object
```

`name`属性有些像Excel中的字段名称有些像，在后期有很重要的作用。

作者：liulixiang1988#gmail.com (#换成@)
参考链接：https://www.coursera.org/learn/hipython/home/welcome
