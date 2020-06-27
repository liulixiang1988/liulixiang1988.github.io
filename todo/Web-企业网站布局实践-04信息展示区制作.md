Title: 企业网站布局实践-04 信息展示区制作
Date: 2015-04-03 11:22
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 结构分析

![结构分析](images/qy-web-05.png)

分析如下：

![结构分析](images/qy-web-06.png)

盒子之间的间距应该是多少呢？

整个盒子宽度是1000px，3个盒子宽度是340、410以及230px(总和为980px)，那么外边据是否就是10px了呢（10*2+980=1000）？不是的，因为每个小盒子左右都有1px的边框，一共6px，因此是(20-6)/2 = 7px.

##2. 实现

首先定义结构：

```
<div class="main">
 <div class="news"></div>
 <div class="course"></div>
 <div class="sidebar"></div>
</div><!-- main结束 -->
```

其次是LESS

```
//定义区域整体高度
.main{
    height:250px;
    margin-top: 5px;
    background-color: #23E;
}

//定义子盒子高度与浮动
.news, .course, .sidebar{
    height: 250px;
    float:left;
}

//定义每个盒子
.news{
    width:340px;
    border: 1px solid #E8E8E8;
    background-color: #DAF;
}

.course{
    width: 410px;
    border: 1px solid #E8E8E8;
    margin: 0 7px;
    background-color: #AEF;
}

.sidebar{
    width:230px;
    background-color: #333;
}
```
