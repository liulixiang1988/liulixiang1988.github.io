Title: Python数据分析(3)-DataFrame
Date: 2015-11-29 15:54
Category: Python
Tags: 数据分析
Author: 刘理想

[TOC]

##1. `DataFrame`简介

`Series`是一维序列，而`DataFrame`是一种二维表结构，它包含一组类似于`index`的有序列，每列可以是不同的值。因此可以把`DataFrame`看作是共享同一个`index`的`Series`集合。

##2. `DataFrame`的创建

创建`DataFrame`的方法与`Series`类似。并且自带index,也可以指定index。

```python
In [1]: import pandas as pd
In [2]: data = {'name': ['Wangdachui', 'Linling', 'Niuyun'], 'pay':[4000, 5000, 6000]}
In [3]: frame = pd.DataFrame(data)
In [4]: frame
Out[4]:
         name   pay
0  Wangdachui  4000
1     Linling  5000
2      Niuyun  6000
```

##3. `DataFrame`的基本操作

```python
In [5]: frame['name'] #列明
Out[5]:
0    Wangdachui
1       Linling
2        Niuyun
Name: name, dtype: object

In [6]: frame.pay #列名
Out[6]:
0    4000
1    5000
2    6000
Name: pay, dtype: int64

In [7]: frame.ix[2] #获取某一行，从0开始
Out[7]:
name    Niuyun
pay       6000
Name: 2, dtype: object
```

取`DataFrame`对象的列和行可获得`Series`

`DataFrame`对象的修改和删除

```python
In [8]: frame['name'] = 'admin' #修改列名
In [9]: frame
Out[9]:
    name   pay
0  admin  4000
1  admin  5000
2  admin  6000

In [10]: del frame['pay'] #删除列
In [11]: frame
Out[11]:
    name
0  admin
1  admin
2  admin
```

##4. `DataFrame`的`name`属性

```python
In [12]: data = {'name': ['Wangdachui', 'Linling', 'Niuyun'], 'pay':[4000, 5000, 6000]}
In [13]: frame = pd.DataFrame(data)
In [14]: frame
Out[14]:
         name   pay
0  Wangdachui  4000
1     Linling  5000
2      Niuyun  6000

In [15]: frame.index.name = 'No' #设置index的name属性
In [16]: frame
Out[16]:
          name   pay
No
0   Wangdachui  4000
1      Linling  5000
2       Niuyun  6000
```

作者：liulixiang1988#gmail.com (#换成@)
参考链接：https://www.coursera.org/learn/hipython/home/welcome