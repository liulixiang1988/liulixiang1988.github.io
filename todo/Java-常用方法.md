Title: Java-常用方法
Date: 2015-04-08 00:30
Category: Java
Tags: Java, Web, JSP
Author: 刘理想

之前一直用C#比较多，最近开始研究Java，两者之间的库设计差别很大，故再次做个总结。

##1. 日期

1.日期格式化

```
import java.text.SimpleDateFormat;
import java.util.Date;

SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日");
String s = sdf.format(new Date());
```

2. 字符串转化为日期

```
SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日");
Date d = sdf.parse("2015年04月13日");
```

##2. 泛型

1.新建泛型

可以再`new`后面直接使用`<>`来初始化，不需要指定类型，如下所示：
```
ArrayList<Crime> crimes = new ArrayList<>();
```