Title: Android学习笔记 - 03 Android四大核心组件之BroadCast
Date: 2014-08-27 09:51
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想
Summary: Android学习笔记 - 03 Android四大核心组件之BroadCast

#Android学习笔记 - 03 Android四大核心组件之BroadCast

##一、BroadCast Receiver静态声明

###1、声明BroadCast Receiver

```java
public class MyBroadCast extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
    }
}
```
一旦广播接收器接收到消息，就会执行`onReceive`方法

###2、Activity发送消息给BroadCast

消息的发送是使用`sendBroadcast`方法，传递给它一个`Intent`
```java
private void btnSendBroadCast_click(){
    Intent i = new Intent(this, MyBroadCast.class);
    i.putExtra("txt", "理想，你好");
    sendBroadcast(i);
}
```

###3、Broadcast Receiver接收消息的处理方法

```java
@Override
public void onReceive(Context context, Intent intent) {
    System.out.println("Receive Message:" + intent.getStringExtra("txt"));
}
```

##二、 Broad Receiver动态声明

###1、声明一个Broadcast Receiver

这里声明广播接收器时同时声明了一个ACTION用来给别的应用或Activity注册使用

```java
public class MyBroadCast2 extends BroadcastReceiver {

    //Action格式：包名+intent.action.类名
    public static final String ACTION="liulx.L04BroadCast.intent.action.MyBroadCast2";

    @Override
    public void onReceive(Context context, Intent intent) {
        System.out.println("接收数据：" + intent.getStringExtra("txt"));
    }
}
```
###2、打开AndroidManifest.xml文件
注释掉` <!--<receiver android:name=".MyBroadCast2"/>-->`

###3、打开Activity

- 添加3个按钮，分别是`btnRegisterBC`，`btnUnregiserBC`, `btnSendBC`
- 添加一个BroadCastReceiver实例 
    private final MyBroadCast2 mybc = new MyBroadCast2() ;
- 按钮的点击方法分别为：

```java
private void btnRegisterBC_click(){
    registerReceiver(mybc, new IntentFilter(MyBroadCast2.ACTION));
}

private void btnUnregisterBC_click(){
    unregisterReceiver(mybc);
}

private void btnSendBroadCast2_click(){
    //这里使用Action来声明Intent
    Intent i = new Intent(MyBroadCast2.ACTION);
    i.putExtra("txt", "Hello, 理想");
    sendBroadcast(i);
}
```

**说明：** 
- 注册Broadcast使用`registerReceiver`，其中的IntentFilter是用之前的Action来声明，起到过滤的作用;
- 取消注册，使用`unregisterReceiver`;
- 发送消息给广播，是用Action声明的Intent.




