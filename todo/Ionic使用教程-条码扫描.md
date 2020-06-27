Title: Ionic使用教程-条码扫描
Date: 2015-05-27 10:32
Category: ionic
Tags: ionic, 移动开发
Author: 刘理想

ionic中与硬件交互部分是使用[ngcordova](http://ngcordova.com/)和cordova来实现。

##1.下载ngcordova并且加入到文件中

到ngcordova的页面下载，然后添加到页面，注意要添加到`cordova.js`之前：

```
<script src="lib/ngCordova/dist/ng-cordova.min.js"></script>
<script src="cordova.js"></script>
```

##2. 安装条码插件

这个插件支持常见的二维码和条码扫描，非常的强大，下面我们来看看如何安装插件。

ngcordova官方给出的安装方法：
```
cordova plugin add https://github.com/wildabeast/BarcodeScanner.git
```

但是我们跳转到对应的github页面：https://github.com/wildabeast/BarcodeScanner/ 发现说官方的地址是：https://github.com/phonegap/phonegap-plugin-barcodescanner 里面给出的安装方法是：

```
phonegap plugin add phonegap-plugin-barcodescanner
```

因为我们用的是ionic和cordova，所以可以使用：

```
cordova plugin add phonegap-plugin-barcodescanner
```


##3. 编写js代码

```
controller('BarcodeCtrl', 
    function ($scope, $cordovaBarcodeScanner) {
        $scope.barcode = "";
        $scope.scanBarcode = function(){
            $cordovaBarcodeScanner.scan().then(function(imageData){
                $scope.barcode = imageData.text;
                console.log("****barcode format:"+imageData.format);
            }, function(error){
                console.log("*****an error occured:"+error);
            });
        };
})
```

代码很简单，使用这个可以完成扫描成功进行赋值，扫描失败则就在控制台打印的功能。

对应的前端页面如下：
```
<div class="list" ng-controller="BarcodeCtrl">
  <div class="item item-input-inset">
    <label class="item-input-wrapper">
      <input type="text" placeholder="条码" ng-model="barcode">
    </label>
    <button class="button button-small" ng-click="scanBarcode()">
      扫描
    </button>
  </div>
</div>
```

至此，你就完成了条码扫描功能。

