Title: Pelican使用多说评论框
Date: 2016-06-21 20:00
Category: Python
Author: 刘理想

[TOC]

使用之前已经讲过，现在我们要使用多说评论框。首先找到模板目录下`comments.html`，添加：

```
{% if DUOSHUO_SITENAME and SITEURL and article.status != "draft" %} 
<section> 
<h1>评论</h1>
<div id="DuoShuoComment" aria-live="polite">
{% include '_includes/DuoShuo_Script.html' %}
</div>
</section> 
{% endif %}
```

然后新建`DuoShuo_Script.html`:
```
{% if DUOSHUO_SITENAME %}
<!-- 多说评论框 start -->
    <div class="ds-thread" data-thread-key="{{ article.url }}" data-title="{{ article.title|striptags }}" data-url="{{ SITEURL }}/{{ article.url }}"></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"liulixiang1988blog"};
    (function() {
        var ds = document.createElement('script');
        ds.type = 'text/javascript';ds.async = true;
        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
        ds.charset = 'UTF-8';
        (document.getElementsByTagName('head')[0] 
         || document.getElementsByTagName('body')[0]).appendChild(ds);
    })();
    </script>
<!-- 多说公共JS代码 end -->
{% endif %}
```