Title: AngularJS学习笔记02-路由、模块和依赖注入
Date: 2015-04-30 22:36
Category: Web
Tags: Angular, Web
Author: 刘理想

内容简介：

- AngularJS的模块化实现
- 一个完整项目结构
- 使用ngRoute进行视图之间的路由
- 一切都是从模块开始的
- ng官方推荐的模块切分方式是什么？
- 模块之间的依赖该怎么做？--依赖注入

##1. 模块

模块就是功能的集合

##2. 目录结构

```
BookStore
|-app
    |-css
    |-img
    |-js
        |-app.js
        |-controllers.js
        |-directives.js
        |-filters.js
        |-services.js
    |-framework
    |-tpls//模板
    |-index.html
|-node_modules
|-package.json
```

##3. ngRoute进行视图之间的路由

```
var bookStoreApp = angular.module('bookStoreApp',
 ['ngRoute', 'ngAnimate', 'bookStoreCtrls', 'BookStoreFilters',
 'bookStoreServices', 'bookStoreDirectives']);

bookStoreApp.config(function($routeProvider) {
    $routeProvider.when('/hello',{
            templateUrl:'tpls/hello.html',
            controller: 'HelloCtrl'
        }).when('/list', {
            templateUrl:'tpls/bookList.html',
            controller: 'BookListCtrl'
        }).otherwise({
            redirectTo:'/hello'
        });
})
```







