Title: Android学习笔记-02 Android 四大核心组件之Service
Date: 2014-08-27 09:50
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想
Summary: Android学习笔记-02 Android 四大核心组件之Service

#Android学习笔记-02 Android 四大核心组件之Service

##一、Service声明
1. 新建基于Service的类
2. 在Mainifest中添加Service
3. 在aty中添加两个按钮btnStart, btnStop
4. 添加方法，让main实现OnClickListener

```java
public void onClick(View v){
    switch (v.getId()){
    case R.id.btnStart:
        break;
    case R.id.btnStop:
        break;
    }
}
```

5. 创建Intent 
serviceIntent = new Intent(this, EchoService.class)
6. 在btn的click方法中startService(serviceIntent)和stopService(serviceIntent)

7. 在service类中重写onCreate() onDestroy()方法
eclipse cmd+d:删除行

##二、绑定服务
1. 创建btn1,2
2. 在click中bindService(serviceIntent, this, Content)，unBind
3. 让class实现
4. 在Service中创建onBind()

```java
public IBinder onBind(Intent arg0){return esb;}
private final EchoServiceBinder esb = new ...{}
public class EchoServiceBinder extends Binder{
    ...
}
```


**注意**：

- 通过start service的服务在aty销毁时不会销毁
- bind service的夫妇在aty销毁时会跟着销毁

##三、service与aty通信

1. 在service中添加timer和tasktimer

```java
public void startTimer(){
    if (timer == null){
        timer = new Timer();
        timerTask = new TimerTask() {
            
            @Override
            public void run() {
                i++;
                System.out.println(i);
                
            }
        };
        
        timer.schedule(timerTask, 1000, 1000);
    }
}

public void stopTimer(){
    if(timer != null){
        timerTask.cancel();
        timer.cancel();
        timer = null;
        timerTask = null;
    }
}
private Timer timer;
private TimerTask timerTask;
```

**这一步与service和activity通信无关，但是这一步可以用来做服务与服务器的通信**

2. 在service的`onCreate`和`unDestroy`方法中启动和停止timer

```java
@Override
public boolean onUnbind(Intent intent) {
    System.out.println("服务-取消服务绑定");
    return super.onUnbind(intent);
}

@Override
public void onCreate() {
    System.out.println("Service Create");
    startTimer();
    super.onCreate();
}
```

3. 在service类中，提供公共方法，以备后面通信时使用，比如：

```java
private int i = 0;

public int getInt(){
    return i;
}
```

4. 在Activity方法中获取Service对象

```java
private EchoService echoService;

@Override
public void onServiceConnected(ComponentName name, IBinder binder) {
    System.out.println("应用已经绑定服务");
    echoService = ((EchoService.EchoBinder)binder).getService();
}
```

5. 最后就可以在Activity中使用Service了

```java
@Override
public void onClick(View v) {
    switch(v.getId()){
    //...
    case R.id.btnUnbindService:
        unbindService(this);
        echoService = null;
        break;
    case R.id.btnGetCurrentNumber:
        if (echoService != null){
            System.out.println("当前获取的数字是："+echoService.getInt());
        }
        break;
        
    }
}
```