Title: AngularJS学习笔记04 路由
Date: 2015-05-03 21:13
Category: Web
Tags: Angular, Web
Author: 刘理想

##Ajax的不足

- Ajax请求不会留下History记录
- 用户无法直接通过URL进入应用中的指定页面（保存书签、链接分享给朋友）
- Ajax对SEO是个灾难

以上是为什么需要前端路由的原因

##路由

需要自己导入angular-route模块。

```
<script src="framework/angular1.3.15/angular.min.js"></script>
<script src="framework/angular1.3.15/angular-route.min.js"></script>
<script>
var bookStoreApp = angular.module('bookStoreApp',
 ['ngRoute', 'bookStoreCtrls']);

bookStoreApp.config(['$routeProvider', function($routeProvider) {
	$routeProvider.when('/hello',{
	        templateUrl:'tpls/hello.html',
	        controller: 'HelloCtrl'
	    }).when('/list', {
	        templateUrl:'tpls/bookList.html',
	        controller: 'BookListCtrl'
	    }).otherwise({
	        redirectTo:'/hello'
	    });
}]);
</script>
```

本节重点在于ui-router的使用，与ngRoute的区别是：
1、可实现路由分开控制多模块页面的各个模块
2、index.html中的视图部分为`<div ui-view></div>`
3、需要依赖ui.router，函数需要引入$stateProvider及$urlRouterProvider
4、语法类似ngRouter
