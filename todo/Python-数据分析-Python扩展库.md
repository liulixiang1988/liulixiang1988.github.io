Title: Python扩展库
Date: 2015-11-25 13:04
Category: Python
Tags: 数据分析
Author: 刘理想
Anaconda和Python(x,y)都自带了下面的这些库。

##1. NumPy

强大的ndarray和ufunc函数。

```
import numpy as np
xArray = np.ones((3, 4))
xArray
Out[3]: 
array([[ 1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.],
       [ 1.,  1.,  1.,  1.]])
```

##2. SciPy

科学计算（插值、积分、优化和图像处理）

```
from scipy import linalg
arr = np.array([[1,2], [3, 4]])
linalg.det(arr) #计算行列式
Out[6]: -2.0
```

##3. Matplotlib

基于NumPy，二维绘图库，简单快速地生成曲线图、直方图和散点图等形式的图。
常用的pyplot是一个简单提供类似Matlab接口的模块。


##4. Pandas

基于SciPy和NumPy，拥有高效的Series和DataFrame数据结构，数据操作和分析能力非常强，拥有能够高效处理大数据集的切片等功能，提供优化库功能读写多种文件格式，如CSV, HDF5等

```
df.head(3) #头3行
df.tail(4) #最后4行
df.sort(columns='score') #按照score列排序
```

作者：liulixiang1988#gmail.com (#换成@)
参考链接：https://www.coursera.org/learn/hipython/home/welcome