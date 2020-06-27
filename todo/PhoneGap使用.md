Title: PhoneGap命令行的使用
Date: 2015-03-29 8:59
Category: 移动开发
Tags: PhoneGap
Author: 刘理想

##1. 环境配置

###1.1 常用框架

- jQuery
- Backbone.js
- dojo
- bootstrap
- kendo UI
- Sencha
- jQuery Mobile
- PhoneJS
- AngularJS
- ICENIUM

###1.2 Node.js

下载并安装nodejs

###1.3 iOS

下载Xcode
打开Xcode 
![](images/1.jpg)
![](images/2.jpg)

###1.4 Android
定义ANDROID_HOME=E:\adt\sdk
将`E:\adt\sdk\platform-tools`和`E:\adt\sdk\tools`加入到环境变量中去。

###1.5 Ant
定义ANT_HOME=C:\apache-ant-1.9.3

##2 安装PhoneGap

###2.1 安装PhoneGap

	npm install -g phonegap

安装过后通过`phonegap -v`看是否安装成功

###2.2 安装cordova

	npm install -g cordova

通过`cordova -v`来查看是否成功

一般安装phonegap即可

##3 使用PhoneGap和Cordova

###3.1 创建项目

	>phonegap create project_name project_id display_name

或者使用cordova创建：

	>cordova create project_name project_id displayname

###3.2 应用结构

- hooks
- platforms:平台
- plugins: 插件
- www：应用代码目录

###3.3 build

	>phonegap build android
	>phonegap build ios

或者使用cordova命令
	
	>cordova platform add android
	>cordova build android

	>cordova platform add ios
	>cordova build ios

	>cordova build

如果想要移除某个平台
	
	>cordova platform remove android

##4 测试

###4.1 iOS测试

	>sudo npm install -g ios-deploy
	>sudo npm install -g ios-sim 
	>phonegap run ios

###4.2 Android测试

	>phonegap run android

###4.3 cordova

	>cordova run android

##5 插件

###5.1 安装插件

	>phonegap plugin add org.apache.cordova.device

或者直接使用url

###5.2 移除插件

	>phonegap plugin remove org.apache.cordova.device

###5.3 查看安装的插件

	>phonegap plugin list

##6 合并

##7 Hooks

before_prepare, before_build, before_install, 等
after_prepare, after_build, after_build, etc

	>cordova prepare

##8 编译部署

###8.1 phoneGap build

	>phonegap remote login -u username -p password
	>phonegap remote build android

###8.2 phonegap serve

	>phonegap serve #能直接运行

##9 下一步

- grunt-phonegap
