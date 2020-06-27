Title: Sass和Compass必备技能之Sass篇-04 Sass语法介绍(高级篇)
Date: 2015-04-17 23:19
Category: Web
Tags: CSS, SASS
Author: 刘理想

##1. @media

sass中的@media跟CSS区别：

sass中的media query可以内嵌在css规则中，在生成CSS的时候，
media query 才会被提到样式的最高级

好处：避免了重复书写选择器或者打乱样式表的流程。

```
@mixin col($width){
    @media(min-width: 768px){
        width: $width;
        float: left;
    }
}
```

##2. @at-root

指定嵌套的内容生成CSS时，在最底层
```
.main-sec{
    font-family: $main-sec-ff;
    @at-root{
        .main-sec-headline{
            font:{
                family: $main-sec-ff;
                size: 16px;
            }
        }

        .main-sec-detail{
            font-size: 12px;
        }
    }
}
```

##3. 逻辑
```
@mixin col($width){
    @if type-of($width) != number {
        @error "$width必须是数值类型，你输入的width是#{$width}";
    }

    @if not unitless($width){
        @if unit($width) != "%"{
            @error  "$width应该是一个百分值，你输入的是#{$width}";
        }
        
    } @else{
        @warn "$width应该是一个百分之，你输入的是#{$width}";
        $width: percentage($width)/100;
    }
}
```

@each @for @while

##4. 输出格式

打开config.rb
```
output_style = :expanded or :nested or :compact or :compressed
```

