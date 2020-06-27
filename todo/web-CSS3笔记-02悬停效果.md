Title: CSS3笔记02-悬停效果
Date: 2015-06-13 20:58
Category: Web
Tags: css3,compass,sass
Author: 刘理想

##1. 首先需要了解几个伪类：

- `:hover`选择器：当鼠标悬停在所指定的元素上时所使用的样式；
- `:active`选择器：当所指定的元素处于激活状态（鼠标在元素上按下还没有松开）时所使用的样式；
- `:focus`选择器：当元素获得光标焦点时使用的样式，主要用在文本框输入文字时使用.

##2. rgba

```
color: #eee;
color: rgba(255, 255, 255, 0.7);
```

rgba增加了透明度，在上面例子中多添加一个`color:#eee;`是为了使不支持rgba的浏览器仍然有color可用。

##3. 文本阴影(text-shadow)

```
a{
    text-shadow: 0 1px 1px rgba(0, 0, 0, 0.5);
}
```

text-shadow的属性如下：
```
text-shadow: h-shadow v-shadow blur color;
```

- h-shadow: 必需。水平阴影的位置。允许负值。
- v-shadow: 必需。垂直阴影的位置。允许负值。
- blur: 可选。模糊的距离。
- color: 可选。阴影的颜色。参阅 CSS 颜色值。

scss:
```
@import "compass/css3/text-shadow";
single-text-shadow($hoff, $voff, $blur, $spread, $color);
```

##4. 悬停与聚焦的样式：

scss：
```
a{
    padding: 5px 15px;
    font-weight: bold;
    color: #eee;
    color: rgba(255, 255, 255, 0.7);
    @include single-text-shadow(0, 1px, 1px, rgba(255, 0, 0, 0.3));

    &:hover,&:focus{
        color: #fff;
        background: rgba(255, 255, 255, 0.15);
    }
}
```

css：
```
#nav li a {
  padding: 5px 15px;
  font-weight: bold;
  color: #eee;
  color: rgba(255, 255, 255, 0.7);
  text-shadow: 0 1px 1px #aaaaaa;
  text-shadow: 0 1px 1px rgba(255, 0, 0, 0.3) #aaaaaa;
}
#nav li a:hover, #nav li a:focus {
  color: #fff;
  background: rgba(255, 255, 255, 0.15);
}
```

##5. 使用border-radius圆角化悬停

scss:
```
@import "compass/css3/border-radius";

border-radius($radius, $vertical-radius)
```

css3:
```
-moz-border-radius: 14px;
-webkit-border-radius: 14px;
border-radius: 14px;
```

##6. 添加过渡

scss:
```
@include single-transition(all, 0.3s, ease-in-out);
```

css3:
```
-moz-transition: all 0.3s ease-in-out;
-o-transition: all 0.3s ease-in-out;
-webkit-transition: all 0.3s ease-in-out;
transition: all 0.3s ease-in-out;
```

##7. 透明度opacity

```
opacity: 0.25;
-ms-fiter: "progid:DXImageTransform.Microsoft.Alpha(Opacity=25)"; /*IE8 hack*/
filter: alpha(opacity=25); /*IE5-7 hack*/
```
