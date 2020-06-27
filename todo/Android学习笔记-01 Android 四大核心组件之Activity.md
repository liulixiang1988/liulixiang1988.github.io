Title: Android学习笔记-01 Android四大核心组件之Actity
Date: 2014-08-27 09:48
Category: Android
Tags: Android学习笔记, Android
Author: 刘理想
Summary: Android学习笔记-01 Android四大核心组件之Actity

#Android学习笔记-01 Android四大核心组件之Actity

##一、Actity生命周期
###1、生命周期的七个方法

**eclipse快捷键alt+/自动提示**

##二、新建Activity

###1、添加Activity类

###2、在manifest中添加

###3、添加layout

###4、重写actiy的`onCreate`方法，使用`setContentView`设置layout

##三、通信
1. 在main的acity中添加button 按钮
2. 在`onCreate`中添加`findViewById(R.id.btnId)`,使用`cmd+1`自动转换
3. 添加点击事件
	
```java
//点击
btn.setOnClickListener(new View.OnClickListern(){
	@Override
	public void onClick(View v){
		Intent i = new Intent(MainAcitity.this, Acty1.class);
		startActivity(i);
	}
})
```
	
##四、关闭Aty
###1、添加按钮
在click中添加`finish()`

##五、在aty间传递数据

两种传递进去，一种传递出来

###1、Intent `i.putExtra("数据名称", "文本内容")`

在第二个Aty中：
`getIntent().getStringExtra("数据名称")`

###2、Intent.putExtra(string, bundle)

```java
//存
Bundle data = new Bundle();
data.putString("txt", "hello")
i.putExtra(data)

//取
Bundle data = getIntent().getExtras();
String txt = data.getString("txt");
```

###3、取得第二个Aty传回的数据

```java
startActivityForResult(i, 0)

protected onActivityResult(int requestCode, int resultCode, Intent data){
	string result = data.getStringExtra("result")
}

//存
Intent i = new Intent();
i.putExtra("result", "hello")
setResult(0, i);
finish();
```