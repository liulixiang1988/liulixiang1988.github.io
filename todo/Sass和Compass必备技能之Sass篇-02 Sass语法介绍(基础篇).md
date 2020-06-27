Title: Sass和Compass必备技能之Sass篇-02 Sass语法介绍(基础篇)
Date: 2015-04-17 22:09
Category: Web
Tags: CSS, SASS
Author: 刘理想

##1. sass和scss形式的相互转换

``` 
$sass-convert main.sass main.scss
或者
$sass-convert main.scss main.sass
```

##2. 创建应用

``` 
$compass create learn-sass-syntax
$cd learn-sass-syntax
$compass watch
```

##3. 语法

SASS CSS默认都是UTF-8，所以写不写 `@charset "UTF-8"` 

编码问题，如果出现中文编译失败，打开 `rubygems\gems\sass\lib\sass\engine.rb` 文件，在require后面添加 `Encoding.default_external=Encoding.find('utf-8')` 。

###3.1 引用@import

使用下划线命名的文件比如_variables.scss，sass称为**局部文件**，是用到其它文件中的。如果在其他文件中引用，请使用:

``` 
@import "variables";
```

这个不是css中的import，css中的import指令有两大弊端，一，必需放在文件行首；
二，只有执行到对应行中才加载（这导致页面加载特别慢）

sass中的import是引入文件到宿主文件, 也就是说在生成CSS的时候就把相关文件导入进来。可以使用配置选项配置路径。

`@import "compass/reset";` 使用normalize来统一，比较好。

使用CSS原声@import的既定规则：

* @import后跟.css结尾的时候
* @import后跟http://开头的字符串
* @import 跟url()
* @import 后带有media queries

注意sass的@import既定规则：

1. 没有文件后缀名的时候，sass会添加.scss或者.sass的后缀
2. 统一目录下，局部文件和非局部文件不能重名
3. 可以同时import多个文件: `@import "variables", "compass/reset";` 

####3.1.1 使用Sass局部文件
Sass局部文件的文件名以下划线开头。Sass不会将局部文件编译成单独的文件，而只把这个文件用于导入。导入局部文件不用输入下划线，比如有一个局部文件 `themes/_night-sky.scss` ，导入这个文件只需要 `@import "themes/night-sky";` 

####3.1.2 默认变量值
使用 `!default` 来声明默认变量，作用是：如果这个变量被声明赋值了，就用它声明的值，否则就用这个默认值。

``` 
$fancybox-width: 400px !default;

.fancybox {
    width: $fancybox-width;
}
```

如果用户在导入你的Sass局部文件之前声明了 `$fancybox-width` 变量，那么你的局部文件中对 `$fancybox-width` 赋值400px就无效。如果用户没有做这样的声明，则 `$fancybox-width` 将默认为400px.

####3.1.3 嵌套导入

比如有一个 `_blue-theme.scss` 的文件，内容如下：

``` 
aside{
    background: blue;
    color: white;
}
```

然后导入到一个CSS规则内：

``` 
.blue-theme { @import "blue-hteme";}
```

被翻译成：

``` 
.blue-theme{
    aside{
        background: blue;
        color: #fff;
    }
}
```

---

###3.2 变量声明

``` 
$headline-ff: Braggadocio, Arial, Verdana, Helvetica, sans-serif;
$main-sec-ff: Arial, Verdana, Helvetica, sans-serif;
```

变量声明中用中划线和下划线的变量是相同的，比如 `$link-color` 和 `$link_color` 是同一个变量。除了变量，后面介绍的混合器也使这样。

###3.3 注释

``` 
/*会被引用到编译后的css文件中*/
//不会被引用到对应的css文件中
```

###3.4 嵌套CSS规则

类似LESS

``` 
.main-sec{
    font-family: $main-sec-ff;

    .headline{
        font-family: $main-sec-ff;
    }
}
```

####3.4.1 父类选择器&

同LESS的&

####3.4.2 群组选择器的嵌套

``` 
.container{
    h1, h2, h3{ margin-bottom: .8em;}
}
```

被翻译成：

``` 
.container h1, .container h2, .container h3{margin-bottom: .8em;}
```

同样的，

``` 
nav, aside {
    a {color: blue}
}
```

被翻译成：

``` 
nav a, aside a{ color: blue;}
```

####3.4.3 子组合选择器和同层组合选择器 : >, + 和 ~

`>` 是直接子元素选择： `article > section` .

`+` 同层相邻组合选择器
`~` 同层全体组合选择器

``` 
article {
    ~ article { border-top: 1px dashed #ccc;}
    > section { background: #eee;}
    dl > {
        dt { color : #333;}
        dd { color : #555;}
    }
    nav + & { margin-top: 0;}
}
```

####3.4.2 属性嵌套

LESS中没有这个，**注意要用冒号**。

``` 
nav{
    border: {
        style: solid;
        width: 1px;
        color: #ccc;
    }
}
```

被翻译成：

``` 
nav{
    border-style: solid;
    border-width: 1px;
    border-color: #ccc;
}
```

甚至可以这样：

``` 
nav{
    border: 1px solid #ccc {
        left: 0px;
        right: 0px;
    }
}
```

翻译为：

``` 
nav{
    border: 1px solid #ccc;
    border-left: 0px;
    border-right: 0px;
}
```
