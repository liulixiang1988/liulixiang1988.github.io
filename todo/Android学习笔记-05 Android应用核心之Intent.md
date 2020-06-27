Title: Android学习笔记 - 05 Android应用核心之Intent
Date: 2014-08-27 09:52
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想
Summary: Android学习笔记 - 05 Android应用核心之Intent

#Android学习笔记 - 05 Android应用核心之Intent

##1 Intent IntentFilter对象介绍

Intent是与基本组件相关联的数据对象，每个基本组件都有一个对应的Intent，Intent指定了对应的组件对象。

IntentFilter用来描述一个Intent

###1.1 使用Component引用对象

```java
Intent i = new Intent();
i.setComponent(new ComponentName("liulx.L06Intent", "liulx.L06Intent.Aty1"));
startActivity(i);
```

`setComponent`用来设置Component

`ComponentName`用来通过包名和类名来创建对象

这种使用`ComponentName`的方法创建Acitivity的方法属于**显示创建对象**

###1.2 使用Action建立对象

####1.2.1 创建Action
如果需要使用Action来启动一个Activity的话，需要给这个Activity配置一个`intent-filter`，打开AndroidManifest.xml文件，找到相应的Acitivity，添加`intent-filter`节点：

```xml
<activity android:name=".Aty1" android:label="Aty1">
    <intent-filter>
        <action android:name="liulx.L06Intent.intent.action.Aty1" />
        <category android:name="android.intent.category.DEFAULT"/>
    </intent-filter>
</activity>
```

`intent-filter`的作用是给Activity添加地址，其中的`action`的名字规则是`包名.intent.action.名称`，它就是Activity的地址。

一般还需添加一个`category`,使用`DEFAULT`即可。

####1.2.2 根据Action来创建对象

在需要创建对象（Activity/Service等)的地方，使用Action来创建：

```java
Intent i = new Intent("liulx.L06Intent.intent.action.Aty1");
```

**注意：**与`setComponent`方法不同，通过action创建的方法叫做**隐式创建**,它不需要知道类的包名和类的全名，只需要知道Action地址就可以了。

##2 实例：打开图片

###2.1 使用默认图片查看工具打开图片

```java
File f = new File("/storage/sdcard0/001.jpg");
Intent i = new Intent(Intent.ACTION_VIEW);
i.setDataAndType(Uri.fromFile(f), "image/*");
startActivity(i);
```

###2.2 使用自己的Activity打开图片

首先，创建一个Activity,并且，它设置对应的AndroidManifest.xml

```java
public class ImageViewer extends Activity {
    ImageView iv;
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        iv = new ImageView(this);
		//使用iv来做ContentView
        setContentView(iv); 
		
		//设置图片的URI
        iv.setImageURI(getIntent().getData());
    }
}
```
注意，`intent-filter`的设置，action name用VIEW，category用DEFAULT, data的mimeType为"image/*"， schema为"file"表示本地文件。

```xml
<activity android:name=".ImageViewer">
    <intent-filter>
        <action android:name="android.intent.action.VIEW"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="image/*" android:scheme="file"/>
    </intent-filter>
</activity>
```

##3 实例：打电话与打开网页

###3.1 打电话

```java
Intent i = new Intent(Intent.ACTION_VIEW);
i.setData(Uri.parse("tel:10086"));
startActivity(i);
```
###3.2 打开网页

```java
Intent i = new Intent(Intent.ACTION_VIEW, Uri.parse("http://www.baidu.com"));
startActivity(i);
```

