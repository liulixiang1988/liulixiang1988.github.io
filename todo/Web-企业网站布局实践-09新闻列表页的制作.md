Title: 企业网站布局实践-09 新闻列表页制作
Date: 2015-04-04 22:12
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 新闻列表页的制作

新闻列表页和首页对比如下：

![对比](images/qy-web-13.png)

主要是两点不同，第一个是新闻列表页焦点图不再具有切换效果，图片大小也不一样；第二个就是内容区是2栏可变长的。

##2. 页面结构

```
<div class="ad" id="picBox"  style="height:243px">
   <img src="images/ad.jpg" alt="ad">
</div><!-- ad结束 -->
<div class="list_main">
    <div class="news_type"></div><!-- news_type结束 -->
    <div class="list_box"></div><!-- list_box结束 -->
</div><!-- list_main结束 -->
```

##3. 内容展示区制作

```
/*--------------------新闻列表页----------------------------*/
.list_main{
    height: 350px;
    background-color: #FFF;
    margin-top: 10px;
}

.news_type{
    width:220px;
    min-height: 350px;
    _height: 350px;
    border: 1px solid #E8E8E8;
    background-color: #F9C;
    float: left;
}

.list_box{
    width: 770px;
    min-height: 350px;
    _height: 350px;
    background-color: #F9F;
    border: 1px solid #E8E8E8;
    float: right;
}
```

##4. 左侧列表制作

```
<div class="news_type">
    <h2>新闻中心</h2>
    <div class="news_type_content">
        <ul>
            <li><a href="list.html">公司新闻</a></li>
            <li><a href="list.html">行业新闻</a></li>
            <li><a href="list.html">媒体报道</a></li>
            <li><a href="list.html">活动专题</a></li>
            <li><a href="list.html">精彩视频</a></li>
            <li><a href="list.html">资源下载</a></li>
            <li><a href="list.html">公司内刊</a></li>
        </ul>
    </div>
</div><!-- news_type结束 -->
```

```
//新闻类别
.news_type{
    width:220px;
    min-height: 350px;
    _height: 350px;
    border: 1px solid #E8E8E8;
    float: left;

    //新闻类别头部标题
    h2{
        height:35px;
        line-height: 35px;
        font-size: 14px;
        font-family: "微软雅黑";
        color: #666;
        background: url(../images/title_bg.gif) repeat-x;
        padding-left: 20px;
    }

    //列表区域
    &_content{
        height: 265px;
        background: url(../images/content_bg.gif) repeat-x;

        li{
            list-style-type: none;
            height: 30px;
            line-height: 30px;

            a {
                color: #666;
                text-decoration: none;
                background: url(../images/li_bg.gif) no-repeat left center; //图像左侧居中
                padding-left: 15px;
                display: block;

                &:hover{
                    color: #009CDF;
                    background: url(../images/li_bg2.gif) no-repeat left center;
                    background-color: #F2F2F2; //即设置背景图像又设置背景颜色
                }
            }
        }
    }
}
```

##5. 新闻列表制作

```
<div class="list_box">
    <h2>新闻中心</h2>
    <div class="news_list">
        <ul>
            <li><span>2015-04-06</span><a href="news.html">【慕客访谈 用户篇】“有为屌丝”在路上</a></li>
            <li><span>2015-04-06</span><a href="news.html">【有奖活动】给父亲的三行书信</a></li>
            <li><span>2015-04-06</span><a href="news.html">《程序猿，请晒出你的童年》活动获奖公告</a></li>
            <li><span>2015-04-06</span><a href="news.html">【慕课访谈】破茧成蝶——美女程序员的蜕变史</a></li>
            <li><span>2015-04-06</span><a href="news.html">【有奖活动】给父亲的三行书信</a></li>
            <li><span>2015-04-06</span><a href="news.html">【慕客访谈 用户篇】“有为屌丝”在路上</a></li>
            <li><span>2015-04-06</span><a href="news.html">《程序猿，请晒出你的童年》活动获奖公告</a></li>
            <li><span>2015-04-06</span><a href="news.html">【慕课访谈】破茧成蝶——美女程序员的蜕变史</a></li>
            <li><span>2015-04-06</span><a href="news.html">【慕客访谈 用户篇】“有为屌丝”在路上</a></li>
            <li><span>2015-04-06</span><a href="news.html">《程序猿，请晒出你的童年》活动获奖公告</a></li>
        </ul>
    </div><!-- news_list结束 -->
</div><!-- list_box结束 -->
```

```
//新闻列表区域
.list_box{
    width: 770px;
    min-height: 350px;
    _height: 350px;
    border: 1px solid #E8E8E8;
    float: right;

        //新闻类别头部标题
    h2{
        height:35px;
        line-height: 35px;
        font-size: 14px;
        font-family: "微软雅黑";
        color: #666;
        background: url(../images/type_bg.png) repeat-x;
        padding-left: 20px;
    }
}

//新闻列表
.news_list{
    .news_list();
}
```

##6. 页码制作

a标签是内联元素，为了使内联元素具有块级元素的一些属性同时使所有的a标签显示在同一行上，我们需要把a标签设置为行内块元素inline-block。

```
<div class="page">
    <a href="#">首页</a>
    <a href="#">&lt;</a>
    <a href="#">1</a>
    <a href="#">2</a>
    <a href="#">3</a>
    <a href="#">...</a>
    <a href="#">&gt;</a>
    <a href="#">末页</a>
</div><!-- page结束 -->
```

```
//页码
.page{
    height: 40px;
    margin-top: 10px;
    text-align: center;

    a{
        border: 1px solid #E8E8E8;
        text-decoration: none;
        margin: 5px;
        padding: 5px 10px;
        display: inline-block; //行内块级元素

        &:link, &:visited{
            color: #000;
        }

        &:hover, &:active{
            color: #FFF;
            background-color: #cc1b1b;
        }
    }
}
```