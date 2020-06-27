Title: Ionic使用教程
Date: 2015-05-12 19:45
Category: ionic
Tags: ionic, 移动开发
Author: 刘理想


Ionic项目主页：http://ionicframework.com/ 它是用来构建混合应用的一款前端框架，使用Cordova和AngularJS，目前中文资料尚且较少。这篇文章主要是在学习中做的一个记录，也希望能为后面的学习者提供一些参考。

##1. 要求

- 安装Node/NPM
- 安装Ruby和SASS
- 了解AngularJS
- 安装Gulp(可选)
- 安装Android SDK和XCode（可选）

##2. ionic开发步骤

###2.1 全局安装cordova和ionic

```
npm install -g cordova ionic
```

###2.2 从模板中创建应用

```
ionic start frontendAwesome sidemenu
cd frontendAwesome
```

其中 frontendAwesome是项目名，sidemenu是模板名

有3个默认模板：sidemenu(侧边菜单), tabs(底部标签), blank(空白)

第一个命令结束后，会自动输出一大堆英文：
```
Creating Ionic app in folder F:\git\WebDesign\ionic\fontendAwesome based on side
menu project

Downloading: https://github.com/driftyco/ionic-app-base/archive/master.zip
[=============================]  100%  0.0s

Downloading: https://github.com/driftyco/ionic-starter-sidemenu/archive/master.z
ip
[=============================]  100%  0.0s

Update config.xml
Initializing cordova project
Updated the hooks directory to have execute permissions
running cordova plugin add org.apache.cordova.device
WARNING: org.apache.cordova.device has been renamed to cordova-plugin-device. Yo
u may not be getting the latest version! We suggest you `cordova plugin rm org.a
pache.cordova.device` and `cordova plugin add cordova-plugin-device`.
Fetching plugin "org.apache.cordova.device" via cordova plugins registry
npm http GET http://registry.cordova.io/org.apache.cordova.device
npm http 304 http://registry.cordova.io/org.apache.cordova.device
Saving plugin to package.json file
Adding since there was no existingPlugin
Updated the hooks directory to have execute permissions
running cordova plugin add org.apache.cordova.console
WARNING: org.apache.cordova.console has been renamed to cordova-plugin-console.
You may not be getting the latest version! We suggest you `cordova plugin rm org
.apache.cordova.console` and `cordova plugin add cordova-plugin-console`.
Fetching plugin "org.apache.cordova.console" via cordova plugins registry
npm http GET http://registry.cordova.io/org.apache.cordova.console
npm http 304 http://registry.cordova.io/org.apache.cordova.console
Saving plugin to package.json file
Adding since there was no existingPlugin
Updated the hooks directory to have execute permissions
running cordova plugin add com.ionic.keyboard
Fetching plugin "com.ionic.keyboard" via cordova plugins registry
npm http GET http://registry.cordova.io/com.ionic.keyboard
npm http 304 http://registry.cordova.io/com.ionic.keyboard
Saving plugin to package.json file
Adding since there was no existingPlugin

Your Ionic project is ready to go! Some quick tips:

 * cd into your project: $ cd fontendAwesome

 * Setup this project to use Sass: ionic setup sass

 * Develop in the browser with live reload: ionic serve

 * Add a platform (ios or Android): ionic platform add ios [android]
   Note: iOS development requires OS X currently
   See the Android Platform Guide for full Android installation instructions:
   https://cordova.apache.org/docs/en/edge/guide_platforms_android_index.md.html


 * Build your app: ionic build <PLATFORM>

 * Simulate your app: ionic emulate <PLATFORM>

 * Run your app on a device: ionic run <PLATFORM>

 * Package an app using Ionic package service: ionic package <MODE> <PLATFORM>

For more help use ionic --help or visit the Ionic docs: http://ionicframework.co
m/docs

+---------------------------------------------------------+
+ New Ionic Updates for May 2015
+
+ The View App just landed. Preview your apps on any device
+ http://view.ionic.io
+
+ Invite anyone to preview and test your app
+ ionic share EMAIL
+
+ Generate splash screens and icons with ionic resource
+ http://ionicframework.com/blog/automating-icons-and-splash-screens/
+
+---------------------------------------------------------+
```

上面介绍了一些基本用法和注意事项，第一次使用的时候应该读一读。

###2.3 build(编译)和emulate(仿真)

```
ionic build ios
ionic emulate ios
ionic serve
#adb安装
adb install -r platforms/android/build/outputs/apk/android-debug.apk
adb lolcat
```

###2.4 自定义ionic样式

文件：scss/ionic.app.scss

###2.5 自定义内容

Angular templating

- Directives
- {{markup}}
- Angular Directives(ng-repeat ng-model)
- Ionic Directives(ion-view, ion-content)
- Filter({{ data|filter }})

www/js/app.js

![app.js](images/ionic-1.PNG)

www/js/controller.js

![app.js](images/ionic-2.PNG)

www/templates/posts.html

![app.js](images/ionic-3.PNG)

www/templates/post.html

![app.js](images/ionic-4.PNG)


###2.6 Ionic CSS组件

http://ionicframework.com/docs/components/
http://ionicons.com/

###2.7 分发

![app.js](images/ionic-5.PNG)

