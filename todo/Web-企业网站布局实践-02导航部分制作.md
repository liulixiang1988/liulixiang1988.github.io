Title: 企业网站布局实践-02导航部分制作
Date: 2015-04-01 23:59
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 分析

我们将导航分为左中右三部分。
其中导航中部分为两部分，分别放置菜单栏和搜索框。
导航栏菜单使用有序列表或无需列表，搜索框使用form表单的input标签。

##2. 实现HTML

添加nav的html
```
<div class="nav">
   <div class="nav_left"></div>
   <div class="nav_mid">
       <div class="nav_mid_left">
           <ul>
               <li><a href="#">首页</a></li>
               <li><a href="#">关于慕课</a></li>
               <li><a href="#">新闻动态</a></li>
               <li><a href="#">课程中心</a></li>
               <li><a href="#">在线课程</a></li>
               <li><a href="#">人才招聘</a></li>
           </ul>
       </div>
       <div class="nav_mid_right">
           <form action="" method="post"><input type="text"></form>
       </div>
   </div><!-- nav_mid结束 -->
   <div class="nav_right"></div>
</div><!-- nav结束 -->
```

##3. 实现LESS

###3.1 实现框架

```
.nav{
    @nav_h:40px;
    @nav_w:10px;
    height:@nav_h;
    clear: both;

    &_left{
        width:@nav_w;
        height: @nav_h;
        background: url(../images/nav_left.jpg) no-repeat;
        // background-image: url(../images/nav_left.jpg);
        // background-repeat: no-repeat;
        float: left;
    }

    &_mid{
        width: 980px;
        background: url(../images/nav_bg.jpg) repeat-x;
        float: left;
    }

    &_right{
        width: @nav_w;
        height: @nav_h;
        background: url(../images/nav_right.jpg) no-repeat;
        float: left;
    }
}
```

###3.2 实现菜单文本

```
&_mid{
    width: 980px;
    background: url(../images/nav_bg.jpg) repeat-x;

    &_left, &_right{
        float:left;
    }

    &_left{
        width:680px;

        li{
            float: left;
            list-style: none; //去掉默认list-style
            width:100px;
            text-align: center; //文本中间对齐
            line-height: 40px; //文本垂直居中，与对应的盒子高度对齐
        }

        a{
            &:link, &:visited{
                text-decoration: none;
                color:#FFF;
                font-size: 16px;
                font-family: "微软雅黑";
            }

            &:hover, &:active{
                text-decoration: none;
                color:#FF0;
                font-size: 16px;
                font-family: "微软雅黑";
            }
        }
    }
```

###实现搜索框

```
.search_text{
    width:190px;
    height:25px;
    padding-right: 25px; //为背景图腾出空间
    background:url(../images/search.jpg) no-repeat right center; //背景图右侧垂直居中
    background-color: #FFF;//设置背景色
    margin-top: 7px; //处于导航中间
    boader: 1px solid #FFF; //边框白色为1
}
```

###更改菜单背景颜色

由于a标签属于内联元素，无高度和宽度属性，因此控制鼠标经过状态改变背景颜色时，仅在有文字的地方显示背景颜色。解决的办法是把a标签变为块级元素.

```
display:block
```

综上，实现一个菜单基本如下：
```
<ul>
    <li><a href="#">全部</a></li>
    <li><a href="#">初级</a></li>
    <li><a href="#">中级</a></li>
    <li><a href="#">高级</a></li>
</ul>
```

```
/*1. 每个列表项的宽度为50px，高度为30px */
ul{
    height:30px;
}

/*2. 列表中的文字在水平和垂直方向上均居中显示*/
li{
    list-style:none;
    float:left;
    width:50px;
    text-align:center;
    line-height:30px;
}

/*3. 超链接初始状态和访问后的状态均为黑色无下划线，鼠标经过和点击时文字均为白色，无下划线，背景颜色为#BE3948。*/
li a {
    display:block;
}
li a:link, li a:visited{
    color:#000;
    text-decoration:none;
}

li a:hover, li a:active{
    color:#FFF;
    text-decoration:none;
    background-color:#BE3948;
}
```

效果图：

![菜单效果图](images/qy-web-03.jpg)