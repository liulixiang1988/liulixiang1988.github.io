Title: 企业网站布局实践-10 新闻详情页的制作
Date: 2015-04-05 20:14
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 页面结构

```
<div class="list_box">
    <h2>新闻中心</h2>
    <h1>【慕客访谈▪用户篇】“有为屌丝”在路上</h1>
    <p  class="list_box_newsInfo">发布人：Admin 发布时间：2015-04-06 浏览3246次</p>
    <div class="list_box_newsContent">
    </div>
</div>
```

##2. 样式

text-indent: 2em; 缩进2个文本尺寸

```
//新闻列表区域
.list_box{
    width: 770px;
    min-height: 350px;
    _height: 350px;
    border: 1px solid #E8E8E8;
    float: right;
    background-color: #FFF;

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

    //新闻标题
    h1{
        font-family: "微软雅黑";
        font-size: 20px;
        text-align: center;
        margin: 10px 0;
    }

    &_newsInfo{
        color: #666;
        text-align: center;
        border-bottom: 1px dotted #666;
    }

    &_newsContent{
        font-size:14px;
        padding: 20px;
        line-height: 25px;
        text-indent: 2em;//缩进2个文本尺寸
    }
}
```