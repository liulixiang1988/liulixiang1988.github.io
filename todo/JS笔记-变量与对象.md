title:JS笔记-变量与对象
date:2015-06-18 16:57
category:Web
tags:JavaScript
author:刘理想

##1. 变量声明

```
var a = a || 7; //如果a有值（严格来说是某个可以被转换为true的值），就直接使用，否则把7赋值给a
```

对比，下面的代码在变量b声明的情况下是对的，如果没有声明，就会引起ReferenceError异常。

```
var a = b || 7; //可能引起ReferenceError异常的危险代码
```

##2. 对象是否存在检验

```
//第一种方法
var a = a || 7;

//第二种方法
var a;
var b = a || 7;

//第三种方法（根据第二种改进）
var a;
var b = a !== undefined ? a : 7;

//第四种方法
if(typeof a !== 'undefined'){
    var b = a;
} else {
    var b = 7;
}
//从这里开始使用变量b

//第五种方法，如果变量在最外层作用域，可以像下面这样
if ('a' in this){
    var b = a;
} else {
    var b = 7;
}
```

第四种方法利用了**JavaScript(ECMAScript)中没有块级作用域的特性**

##3. 属性是否存在检验

```
//第一种方法，避免出现TypeError异常
obj.x && obj.x.y

//第二种方法，检测是否存在某一属性，使用in运算符
if ('a' in this){}
```

