Title: 企业网站布局实践-06 课程中心制作
Date: 2015-04-03 21:33
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 结构分析

![课程中心](images/qy-web-09.png)

##2. 网页结构

```
<div class="course">
 <div class="title">
    <h2 class="title_left">课程中心</h2><span class="title_right"><a href="news.html">More&gt;&gt;</a></span>
 </div><!-- title结束 -->
 <div class="course_pic">
     <img src="images/css.jpg" alt="css"><h2><a href="news.html">CSS圆角进化论</a></h2><p>CSS圆角的实现，经历了三大发展阶段：背景图片方式、CSS2.0+HTML标签模拟、CSS3.0圆角属性）。本案例详细讲解每一种的实现方式，并对实现的优缺点进行对比分析。</p>
 </div><!-- course_pic结束 -->
 <div class="course_type">
     <ul>
         <li>PHP开发</li>
         <li>前端开发</li>
         <li>JAVA开发</li>
         <li>Android开发</li>
     </ul>
 </div><!-- course_type结束 -->
</div><!-- course结束 -->
```

course分为3部分：

- title部分与新闻中心类似
- course_pic图片课程部分与新闻中心也类似
- course_type部分就是一个列表

##3. LESS实现

1、首先是course整个框架，在之前已经定义过

```
.news, .course, .sidebar{
    height: 250px;
    float:left;
}
```

2、title部分与新闻中心的title是使用同一套

```
//标题
.title{
    height: 35px;
    border-bottom: 2px solid #E8E8E8;
    
    //新闻中心
    &_left{
        width: 70%;
        line-height: 35px; //垂直居中
        font-family: "微软雅黑";
        font-size: 14px;
        font-weight: bold;
        color: #786F66;
        float: left; 
        padding-left: 20px;
    }

    //更多
    &_right{
        width:20%;
        line-height: 35px;
        float:right;
        text-align: right;

        a{
            text-decoration: none;
            color: #999999;
            font-family: "宋体";
            font-size: 10px;
            font-weight: normal;
            padding-right: 10px;
        }
    }
}
```

3、图片

因为这部分与新闻中心的有些类似，只是高度不同，因此，我们将它提取为一个通用LESS

```
//图片内容
.pic_content(@pic_height){
    height:@pic_height;
    margin-top: 10px;
    line-height: 22px; //设置行高
    img{
        float: left;
        margin: 0 5px;
    }

    a{
        color:#C00;
        text-decoration: none;
    }
}

//调用
&_pic{
    .pic_content(120px);
}
```

4、课程类型

先固定高度与宽度，居中，设置背景图。

然后再设置其中的li，使其居中，左右间距为15px

```
&_type{
    height: 37px;
    width: 372px;
    background: url(../images/product_type_bg.jpg) no-repeat;
    margin: 20px auto;

    li{
        list-style-type: none;
        float: left;
        line-height: 37px;
        margin: 0 15px;
        text-align: center;
    }
}
```

效果图如下：

![images/qy-web-10.png](images/qy-web-10.png)


