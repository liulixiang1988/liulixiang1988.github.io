Title: AngularJS学习笔记03 双向数据绑定
Date: 2015-05-01 0:20
Category: Web
Tags: Angular, Web
Author: 刘理想

内容简介：

- 简单的例子
- 取值表达式和ng-bind指令
- 双向绑定的典型场景-表单
- 动态切换标签样式
- ng-show, ng-hide
- ng-class
- ngAnimate

## 双向数据绑定

```
<!DOCTYPE html>
<html ng-app="UserInfoModule">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <title>数据绑定</title>
        <link rel="stylesheet" href="stylesheets/bootstrap.min.css">
    </head>
    <body ng-app>
        <div class="panel panel-primary">
            <div class="panel-heading">双向数据绑定</div>
            <div class="panel-body">
                <div class="row">
                    <div class="col-md-12">
                        <form class="form-horizontal" ng-controller="UserInfoCtrl">
                            <div class="form-group">
                                <label class="col-sm-2 control-label">Email</label>
                                <div class="col-sm-10">
                                    <input type="text" class="form-control"  placeholder="邮箱" ng-model="userInfo.email">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-sm-2 control-label">密码</label>
                                <div class="col-sm-10">
                                    <input type="password" class="form-control"  placeholder="请输入密码" ng-model="userInfo.password">
                                </div>
                            </div>
                            <div class="form-group">
                                <div class="col-md-offset-2 col-md-10">
                                    <div class="checkbox">
                                        <label>
                                            <input type="checkbox" ng-model="userInfo.autoLogin">自动登录
                                        </label>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group">
                                <div class="col-md-offset-2 col-md-10">
                                    <label>
                                        <button ng-click="getFormData()" class="btn btn-default">获取FormData</button>
                                        <button ng-click="setFormData()" class="btn btn-default">设置FormData</button>
                                        <button ng-click="resetFormData()" class="btn btn-default">重置FormData</button>
                                    </label>
                                </div>
                            </div>
                        </form>
                    </div>
                </div>
                
            </div>
        </div>
        <script src="js/angular.min.js"></script>
        <script>
        var userInfoModule = angular.module('UserInfoModule', []);
        userInfoModule.controller('UserInfoCtrl', ['$scope', function ($scope) {
            $scope.userInfo={
                email: "550488300@qq.com",
                password: "550488300",
                autoLogin: true
            };
            $scope.getFormData = function(){
                console.log("email:"+$scope.userInfo.email+";"+"password:"+$scope.userInfo.password);
            };
            $scope.setFormData = function(){
                $scope.userInfo.email = "liulixiang1988@gmail.com";
                $scope.userInfo.password = "liulixiang1988";
                $scope.userInfo.autoLogin = false;
                console.log("email:"+$scope.userInfo.email+";"+"password:"+$scope.userInfo.password);
            };
            $scope.resetFormData = function(){
                $scope.userInfo.email="550488300@qq.com";
                $scope.userInfo.password="550488300";
                $scope.userInfo.autoLogin = true;
                console.log("email:"+$scope.userInfo.email+";"+"password:"+$scope.userInfo.password);
            };
        }])
        </script>
    </body>
</html>
```


## ng-class

```
div ng-class='{error: isError, warning:isWarning}'>
```

当isError为ture时，使用.error, 当isWaring为ture时，使用.waring

##ng-show， ng-hide

```
<div ng-show='menuState.show'>
```

当`menuState.show`为true时显示，为false时隐藏;
ng-hide与ng-show相反，为true时隐藏，为false时显示。








