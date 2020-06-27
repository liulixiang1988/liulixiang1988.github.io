Title: Android学习笔记 - 04 Android四大核心组件之Content Provider
Date: 2014-08-27 09:52
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想
Summary: Android学习笔记 - 04 Android四大核心组件之Content Provider

#Android学习笔记 - 04 Android四大核心组件之Content Provider

##一、概述

Content Provider用于应用之间共享数据，比如读取联系人数据，或者自己应用的数据共享给其它的应用

如果要共享数据，必须指定一个URI

本节主要读取其它应用的数据，这里以联系人数据为例。

##二、用法

###1、读取联系人，使用查询，返回游标，然后遍历游标
```java
Cursor c = getContentResolver().query(ContactsContract.Contacts.CONTENT_URI, null, null, null, null);

while(c.moveToNext()){
    System.out.println(">>>>>>>" + c.getString(c.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME)) +
            "\t" + c.getString(c.getColumnIndex(ContactsContract.Contacts.PHONETIC_NAME)));
}
```

###2、在AndroidManifest.xml的<mainifest>节点下添加读取权限：
	<uses-permission android:name="android.permission.READ_CONTACTS" />