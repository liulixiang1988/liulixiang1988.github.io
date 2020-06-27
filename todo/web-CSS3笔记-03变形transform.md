Title: CSS3笔记03-变形transform
Date: 2015-06-13 22:18
Category: Web
Tags: css3,compass,sass
Author: 刘理想

##1. 缩放scale

```
transform:scale(1.2);
```

`transform-origin`:缩放开始位置
```
transform-origin: bottom left;
```

##2. box-shadow 阴影

用法同text-shadow

##3. 旋转rotate

```
transform:scale(1.2)rotate(-10deg)
```

##4. 倾斜skew

```
transform:scale(1.2)skew(-5deg, 30deg);
```

skew的参数分别是x坐标角度和y坐标角度，也可以给相同的角度`skew(30deg)`

##5. 平移translate

```
transform:scale(1.2)translate(20px, 40px);
```
默认是向右和向下，如果是负数，方向相反