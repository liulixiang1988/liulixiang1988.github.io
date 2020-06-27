Title: 企业网站布局实践-01页面头部制作
Date: 2015-04-01 09:05
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 总体分析

网站的架构采用自顶向下渐进明细的过程进行规划。

网站首页设计如下：
![](images/qy-web-01.png)

##2. 页面头部制作

1、居中 

```
margin: 0 auto;
```

2、更改li的图标

```
list-style-image: url(../images/li_bg.gif);
```

去除li的默认图标

```
list-style-type:none;
//或者
list-style:none
```

3、浮动菜单右侧

```
float: right;
```

4、菜单的宽度直接设置li的width

5、文本垂直居中
设置元素文字行高等于盒子的高度

```
line-height: 27px;
```

6、去掉a链接的下划线
```
text-decoration: none;
```
设置链接正常状态：

```
a{
    &:link, &:visited{
        color:#8E8E8E;
        text-decoration: none;
    }

    &:hover, &:active{
    color:#D90;
    text-decoration: none;
    }
}
```


7、文字与图片水平中间对齐
```
img{
    vertical-align: middle;
}
```

效果如下：
![](images/qy-web-02.png)

代码如下：
html

```
<div class="top">
    <div class="top_content">
        <ul>
            <li><a href="#">加入我们</a></li>
            <li><a href="#">添加收藏</a></li>
            <li><a href="#">设为首页</a></li>
        </ul>
    </div>
</div><!-- top结束 -->

<div class="wrap">
    <div class="logo">
        <div class="logo_left">
            <img src="images/logo.jpg" alt="慕课网">
        </div>
        <div class="logo_right">
            <img src="images/tel.jpg" alt="服务热线">24小时服务热线:<span class="tel">123-456-7890</span>
        </div>
    </div><!-- logo结束 -->
</div><!-- wrap结束 -->
```

对应的LESS

```
.top{
    width: 100%;
    height:27px;
    background: url(../images/top_bg.jpg) repert-x;

    &_content{
        width:1000px;
        margin: 0 auto;

        li{
            list-style-image: url(../images/li_bg.gif);
            float: right;
            width: 80px;
            line-height: 27px;
        }

        a{
            &:link, &:visited{
                color:#8E8E8E;
                text-decoration: none;
            }

            &:hover, &:active{
            color:#D90;
            text-decoration: none;
            }
        }

        
    }
}

.wrap{
    width:1000px;
    margin: 0 auto;
    background-color: #FFF;

    .logo{
        height: 80px;

        &_left{
            width:200px;
            float:left;
        }

        &_right{
            width:300px;
            float:right;
            height:28px;
            margin-top:30px;
            color:#8E8E8E;

            img{
                vertical-align: middle;
                margin-right: 10px;
            }
        }
    }
}

.tel{
    font-family: "微软雅黑";
    font-size: 16px;
    color: #C00;
}
```




