Title: 企业网站布局实践-07 侧边栏制作
Date: 2015-04-04 21:01
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 结构分析

![侧边栏](images/qy-web-11.png)

从图中我们知道，除标题外，其余内容大致分成上下两部分。分别是上面的视频和下面的广告。下面我们来实现这个效果。

##2. 文档结构

```
<div class="sidebar">
    <div class="video">
        <div class="title">
           <h2 class="title_left">媒体聚焦</h2><span class="title_right"><a href="news.html">More&gt;&gt;</a></span>
        </div><!-- title结束 -->
        <p class="video_content">
            <embed src="http://player.youku.com/player.php/sid/XNjkzMDE5MTUy/v.swf" allowfullscreen="true" quality="high" width="220" height="140" align="middle" allowscriptaccess="always" type="application/x-shockwave-flash">
        </p><!-- video_content结束 -->
    </div><!-- video结束 -->
    <div class="service">
        <img src="images/app.jpg" alt="app">
    </div><!-- services结束 -->
</div><!-- sidebar结束 -->
```

##3. LESS实现

```
.sidebar{
    width:230px;

    .video{
        height: 185px;
        margin-bottom: 10px;
        border: 1px solid #E8E8E8;

        &_content{
            height:150px;
            text-align: center;
        }
    }
}

.service{
    height:55px;
    border: 1px solid #E8E8E8;
}
```
