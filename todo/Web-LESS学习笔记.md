Title: LESS学习笔记
Date: 2015-03-29 8:59
Category: Web
Tags: CSS, LESS
Author: 刘理想

##1. LESS注释

```
//不会被编译到CSS中
/*编译到CSS*/
```

##2. 变量

```
//定义
@test_width: 10px;

.box{
	width:@test_width; //调用
}
```

##3. 混合

```
//第一种
.box1{
	border: 5px solid pink;
}

.box2{
	.box1;
}

//第二种

.box3(@border_width){
	border: @border_width solid yellow;
}

.box3_1{
	.box3(3px);
}

//第三种
.box4(@border_width:1px){
	border: @border_width solid yellow;
}

.box4_1{
	.box4();
}
```

##4. 匹配模式

```
.traingle(top, @w:5px, @c:#ccc){
	
}

.traingle(bottom, @w:5px, @c:#ccc){
	
}

//通用匹配，不论匹配哪一个都会把这个给带上
.traingle(@_, @w:5px, @c:#ccc){
	
}

.box{
	.traingle(top);//也会调用通用匹配哦
}


```


##5. 嵌套

```
ul{
	//...

	li{

	}

	a {
		//...
		&:hover{

		}
	}
}
```

