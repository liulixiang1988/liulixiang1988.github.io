Title: AngularJS学习笔记01-MVC
Date: 2015-04-30 20:51
Category: Web
Tags: Angular, Web
Author: 刘理想

##0. 第一个AngularJs应用

```
<!DOCTYPE html>
<html ng-app>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>AngularJs Demo</title>
    <script src="bower_components/angularjs/angular.min.js"></script>
</head>
<body>
    <input type="text" ng-model="name"><br>
    <p>hello, {{name}}</p>
</body>
</html>
```

##1. 为什么需要MVC?

MVC只是手段，终极目标是模块化与复用。

##2. 控制器(ng-controller)

```
<body ng-app="helloAngularApp">
    <div ng-controller="HelloCtrl">
        <p>{{greeting.text}},world!</p>
        <button ng-click="test()">test</button>
    </div>

    <script src="js/angular.min.js"></script>
    <script>
    var helloAngularApp = angular.module('helloAngularApp', []);
    helloAngularApp.controller('HelloCtrl', function ($scope) {
            $scope.greeting = {
                text:"Hello"
            };
            $scope.test = function(){
                alert("test");
            };
    })
    </script>
</body>
```


###2.1 注意点

1. 不要视图去复用Controller，一个控制器只负责一小块视图；
2. 不要在Controller中操作DOM，这不是控制器的职责
3. 不要在Controller里做数据格式化，ng有很好用的表单格式
4. 不要在Controller里做数据过滤，ng有$filter服务
5. 一般来说，Controlle是不会相互调用的，控制器之间的交互会通过事件进行。

##3. 模型Model(ng-model)

```
<body ng-app>
    <div>
        <input type="text" ng-model="greeting.text">
        <p>{{greeting.text}},world!</p>
    </div>
    <script src="js/angular.min.js"></script>
</body>
```

##4. 视图View

通过指令实现

```
<body ng-app="helloAngularApp">
    <div>
        <hello></hello>
    </div>

    <script src="js/angular.min.js"></script>
    <script>
    var helloAngularApp = angular.module('helloAngularApp', []);
    helloAngularApp.directive('hello', function () {
        return {
            restrict: 'E',
            template:'<div>大家好!</div>',
            replace: true
        };
    })
    </script>
</body>
```

##5. $scope

**注意：AngularJS的MVC是借助于$scope实现的!!!**

```
<body ng-app="helloAngularApp">
    <div ng-controller="GreetCtrl">
        Hello, {{name}}!
    </div>
    <div ng-controller="ListCtrl">
        <ol>
            <li ng-repeat="name in names">姓名：{{name}} 部门：{{department}}</li>
        </ol>
    </div>

    <script src="js/angular.min.js"></script>
    <script>
    var helloAngularApp = angular.module('helloAngularApp', []);
    helloAngularApp.controller('GreetCtrl', function ($scope, $rootScope) {
        $scope.name = "world";
        $rootScope.department = "Angular";
    });

    helloAngularApp.controller('ListCtrl', ['$scope', function ($scope) {
        $scope.names = ["刘理想", "福宝"];
    }]);
    </script>
</body>
```

###5.1 事件在scope上的传播

```
<body ng-app="helloAngularApp">
    <div ng-controller="HelloCtrl">
        Root Scope
        <tt>My Event:</tt> count:{{count}}
        <ul>
            <li ng-repeat="i in [1]" ng-controller="HelloCtrl">
                <button ng-click="$emit('MyEvent')">$emit("MyEvent")</button>
                <button ng-click="$broadcast('MyEvent')">$broadcast("MyEvent")</button>
                <br>
                Middle scope:
                <tt>My Event:</tt> count:{{count}}
                <ul>
                    <li ng-repeat="i in [1, 2]" ng-controller="HelloCtrl">
                        Leaf scope:
                        <tt>My Event:</tt> count: {{count}}
                    </li>
                </ul>
            </li>
        </ul>
    </div>

    <script src="js/angular.min.js"></script>
    <script>
    var helloAngularApp = angular.module('helloAngularApp', []);
    helloAngularApp.controller('HelloCtrl', function ($scope) {
            $scope.count = 0;
            $scope.$on('MyEvent', function(){
                $scope.count++;
            });
    })
    </script>
</body>
```

$scope是什么：

- $scope是一个POJO(Plan Old JavaScript Object)
- $scope 提供了一些工具方法$watch()/$apply()
- $scope是表达式的执行环境(或称为作用域)
- $scope是一个树形结构，与DOM标签平行
- 子$scope对象会继承父$scope上的属性和方法
- 每一个Angular应用都有一个根$scope对象，一般位于ng-app上
- $scope可以传播事件，可以向上传播，也可以向下传播
- $scope不仅是MVC的基础，也是后面实现双向数据绑定的基础
- 可以使用angular.element($0).scope()进行调试

##6. $scope生命周期

- 创建
- 注册监控
- 检测模型变化
- 发出事件
- 销毁


