Title: 企业网站布局实践-03焦点图制作
Date: 2015-04-02 01:19
Category: Web
Tags: CSS, HTML, LESS
Author: 刘理想

##1. 使用myFocus焦点图库

http://demo.jb51.net/js/myfocus/download.html

选择demo演示下载解压，拷贝js文件夹与当前js文件夹合并。

###1.1 引入myFocus库文件和样式文件

```
<script src="js/myFocus-2.0.1.min.js"></script>
<script src="js/mf-pattern/mF_YSlider.js"></script>
<link rel="stylesheet" href="js/mf-pattern/mF_YSlider.css">
```

###1.2 构建页面中使用的图片

首先添加图片
```
<div class="ad" id="picBox">
 <ul>
     <li><img src="images/ad2.jpg" alt=""></li>
     <li><img src="images/ad3.jpg" alt=""></li>
     <li><img src="images/ad4.jpg" alt=""></li>
 </ul>
</div><!-- ad结束 -->
```

然后定义ad的样式，我们知道图片的高度是310px
```
.ad{
    height: 310px;
}
```

此时预览图片可以看到图片溢出了ad取，需要增加`overflow:hidden`
```
.ad{
    height: 310px;
    overflow: hidden;
}
```

###1.3 增加切换动作

给.ad添加id，并给里面添加一层`<div class="pic">`。

```
 <div class="ad" id="picBox">
     <div class="pic">
         <ul>
             <li><img src="images/ad2.jpg" alt=""></li>
             <li><img src="images/ad3.jpg" alt=""></li>
             <li><img src="images/ad4.jpg" alt=""></li>
         </ul>
     </div>
 </div><!-- ad结束 -->
```

 添加js

 ```
<script>
    myFocus.set({
        id:'picBox'
    });
</script>
 ```

###1.4 添加loading提示

刚加载时，可能会发生白屏现象，我们可以给焦点图添加loading动画。

```
<div class="ad" id="picBox">
 <div class="loading"><img src="images/loading.gif" alt="图片加载中"></div>
 <div class="pic">
     <ul>
         <li><img src="images/ad2.jpg" alt=""></li>
         <li><img src="images/ad3.jpg" alt=""></li>
         <li><img src="images/ad4.jpg" alt=""></li>
     </ul>
 </div>
</div><!-- ad结束 -->
```

运行效果如下：

![焦点图](images/qy-web-04.png)