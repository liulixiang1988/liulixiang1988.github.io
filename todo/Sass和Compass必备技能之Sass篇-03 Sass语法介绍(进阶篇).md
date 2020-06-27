Title: Sass和Compass必备技能之Sass篇-03 Sass语法介绍(进阶篇)
Date: 2015-04-17 22:46
Category: Web
Tags: CSS, SASS
Author: 刘理想

##1. 变量操作

变量操作有两种方式，第一种是直接操作，第二种是通过函数。

通过函数：
- 跟代码块无关的函数，多是自己内置函数，称functions
- 可重用的代码块，称mixin: @include和 @extand的方式来应用，

运算符： `>= <= > < == != ()`

CSS3增加了HSL的颜色，SASS会自动转换HSL为RGB,完美解决兼容。

sass支持@function来声明函数。

##2. 混合器mixin和include

`@mixin`来定义混合器
`@include`来使用混合器
```
//声明
@mixin col-6{
    width: 50%;
    float: left;
}

//调用
.webdemo-sec {
    @include col-6(); //括号可以省略

    &:hover{
        background-color: #F5F5F5;
    }
}
```

###2.1 何时使用混合器
如果你能找到一个很好的短名字来描述属性样式，比如`rounded-corners`， `fancy-font`或者`no-bullets`，那么往往能够构造一个合适的混合器。
混合器和类名的区别是，类名往往是语义化的，而混合器是展示性的描述。

CSS中class最好使用语义化的命名。IE6不支持多个class。

###2.2 给混合器传参

带参数以及默认参数：
```
@mixin col($width:50%){
    width: $width;
    float: left;
}

.webdemo-sec {
    @include col(30%);

    &:hover{
        background-color: #F5F5F5;
    }
}
```

默认参数也可以这样：
```
@mixin link-color($normal, $hover: $normal, $visited: $hover){
    color: $normal;
    &:hover{ color: $hover;}
    &:visited{ color: $visited;}
}
```

调用可以这样：`@include link-color(red)`， `$hover`和`$visited`也会被自动赋值为`red`

##3. 使用选择器继承来精简CSS
选择器继承是说一个选择器可以继承了另一个选择器定义的所有样式，通过`@extend`来实现。
```
.error{
    color: #f00;
}

.serious-error{
    @extend .error;
    border: 1px solid #f00;
}
```

任何跟`.error`有关的组合选择器也会被`.serious-error`以组合选择器的形式继承。
```
.error a { //应用到.serious-error a
    color: red;
    font-weight: bold;
}

h1.error{ //应用到h1.serious-error
    font-size: 1.3em;
}
```

###3.1 何时使用继承
当一个元素拥有的类似另一个类的细化，这时候使用继承比较合适。 
extend两个知识点：
1. extend不可以继承选择器序列
2. %仅用来被继承，如`%error`，则不会输出到css中。


