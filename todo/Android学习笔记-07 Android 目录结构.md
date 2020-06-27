Title: Android学习笔记-07 Android 目录结构
Date: 2015-04-19 19:09
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想

src存放源代码
gen存放系统自动生成的配置文件
Android4.2下含android.jar包文件，所需的SDK
assets存放资源文件，不会自动生成id且不会自动占用空间，没有应用时不会占空间
bin存放被编译生成的可执行文件.apk
libs里放一些jar包
res存放所有资源。如图片等，res有多大，生成的APK就有多大
drawable存放不同密度的图片资源，不同分辨率的设备，不同分辨率的图片
layout存放布局文件。xml文件
values存放字符串，主题，颜色，样式等资源，对应3.0版本，v11对应3.0和v14对应4.0
menu菜单布局文件
androidmanifest.xml清单文件，先执行的清单文件，配置一些与应用有关的重要信息，包含包名，权限，程序组件等等

![images/android-07-01.jpg](images/android-07-01.jpg)