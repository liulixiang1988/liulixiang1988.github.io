Title: 企业网站布局实践-05 新闻中心制作
Date: 2015-04-03 14:00
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1、结构分析

![新闻中心](images/qy-web-07.png)

从上到下可以分为新闻标题、图片新闻和图片新闻。

##2、创建结构

新闻中心分为标题、图片新闻和新闻列表3部分。
其中，新闻列表还是使用无序列表。
```
<div class="news">
 <div class="title">
     <h2>新闻中心</h2><a href="news.html">More&gt;&gt;</a>
 </div><!-- title结束 -->
 <div class="pic_news">
     <img src="images/news.jpg" alt=""><h2>520 慕女神喊你来表白！</h2>
     <p>
         活动时间：5月20日—5月25日<br>获奖公布时间：5月26日<br>Learn More>>
     </p>
 </div><!-- pic_news结束 -->
 <div class="news_list">
     <ul>
         <li><a href="news.html">【慕客访谈 用户篇】“有为屌丝”在路上</a><span>2014-06-01</span></li>
         <li><a href="news.html">【有奖活动】给父亲的三行书信</a><span>2014-06-01</span></li>
         <li><a href="news.html">《程序猿，请晒出你的童年》活动获奖公告</a><span>2014-05-30</span></li>
         <li><a href="news.html">【慕课访谈】破茧成蝶——美女程序员的蜕变史</a><span>2014-05-28</span></li>
     </ul>
 </div><!-- news_list结束 -->
</div><!-- news结束 -->
```

##3、创建新闻中心标题的CSS

新闻标题是title节

```
.news{
    width:340px;
    border: 1px solid #E8E8E8;
    background-color: #FFF;

    .title{
        height: 35px;
        border-bottom: 2px solid #E8E8E8;

        //“新闻中心”几个字左对齐
        &_left{
            width: 70%;
            line-height: 35px; //居中
            font-family: "微软雅黑";
            font-size: 14px;
            font-weight: bold;
            color: #786F66;
            float: left; 
            padding-left: 20px;
        }

        //"more"右对齐
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
}
```

说明：
- `text-decoration`去除a的下划线
- `line-height`设置行高可以设置居中对齐

运行结果如下：

![新闻中心标题](images/qy-web-08.png)

##4、新闻中心之图片新闻


```
//图片新闻
.pic_news{
    height:80px;
    margin-top: 10px;
    line-height: 22px; //设置行高
    img{
        float: left;
        margin-top: 0 5px;
    }

    a{
        color:#C00;
        text-decoration: none;
    }
}
```

说明：
- `line-height`设置行高可以增大文字行间距。

##5、新闻中心之新闻列表

```
//新闻列表
.news_list(){
    margin-top: 20px;
    li{   
        list-style-type: none; //去除默认样式
        background: url(../images/list.jpg) no-repeat; //添加新样式
        padding-left: 10px; //保持图标与文字一段距离
        margin: 8px; //设置周围间距
        border-bottom: 1px dotted #CCC; //每条新闻下加虚线
        //新闻链接样式
        a{
            &:link, &:visited{
                text-decoration: none;
                color: #000;
            }

            &:hover, &:active{
                text-decoration: none;
                color: #F00;
            }
        }
        //日期
        span{
            color: #999;
            float: right;
        }
    }
}
```

注意li符号可以通过background、padding-left、list-style-type 3者结合进行设置，也可使用list-style-image进行设置。

list-style-type可以设置列表项目符号为：none无项目符号，disc实心圆，circle空心圆，square实心方块等但是不可以设置图片为项目符号。

