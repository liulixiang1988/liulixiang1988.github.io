Title: RequireJS使用笔记
Date: 2015-07-22 15:30
Category: Web
Tags: JavaScript, RequireJS, 模块化
Author: 刘理想

RequireJS的项目地址在：http://requirejs.org/

RequireJS常用的方法：
1. requirejs.config 给模块指定别名
2. requirejs 引入模块
3. define 编写模块

##入口文件

```
   <script src="js/require.js" type="text/javascript" data-main="js/main"></script>
```
requirejs加载完之后，立即引入的文件由`data-main`来定义。这里文件为`js/main.js`

我们创建的main.js如下：
```
//定义别名
requirejs.config({
    paths:{
        jquery: 'jquery-1.11.3.min'
    }
})
//引用模块
requirejs(['jquery', 'validate'], function($, validate){
    console.log(validate.isEqual(1, 2));
});
```

其中`validate`在`validate.js`中定义:
```
define(['jquery'], function($){

    return {
        isEmpty: function(){},
        checkLength: function(){},
        isEqual: function(str1, str2){
            return str1 === str2;
        }
    }
});
```

