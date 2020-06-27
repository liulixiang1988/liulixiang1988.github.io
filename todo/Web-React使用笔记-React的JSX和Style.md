Title: React使用笔记-React的JSX和Style
Date: 2015-11-27 20:56
Category: Web
Tags: JavaScript,React
Author: 刘理想

[TOC]

##1. 文件基本结构

```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title></title>
    <link rel="stylesheet" href="">
    <!--react.js-->
    <script src="js/react.js"></script>
    <!--解析JSX-->
    <script src="js/JSXTransformer.js"></script>
</head>
<body>
    <div id="container"></div>
    
    <!--JSX的声明需要是type="text/jsx"-->
    <script type="text/jsx">
    var Hello = React.createClass({
        render: function(){
            return <div>Hello, {this.props.name}</div>;
        }
    });
    React.render(<Hello name="world"/>, 
        document.getElementById('container'));
    </script>
</body>
</html>
```

注意：JSX的type是`text/jsx`.

##2. 添加样式

###2.1 使用外部CSS样式

注意，在JSX中不能直接给components添加`class`，因为`class`在ES6中是关键字，并且在ES6之前也是保留字，使用`className`来代替。
```
<script type="text/jsx">
var Hello = React.createClass({
    render: function(){
        return <div className="alert-text">Hello, {this.props.name}</div>;
    }
});
React.render(<Hello name="world"/>, 
    document.getElementById('container'));
</script>
```

###2.2 使用内联CSS样式

JSX中内联样式需要使用字典来表示，并且使用驼峰命名法代替原有的`font-size`等格式的样式，比如`fontSize`来代替`font-size`等。
**注意**，如果样式出现错误，可以通过控制台来查看错误。

```
<script type="text/jsx">
var Hello = React.createClass({
    render: function(){
        return <div style={{color:'red'}}>Hello, {this.props.name}</div>;
    }
});
React.render(<Hello name="world"/>, 
    document.getElementById('container'));
</script>
```

`style={{color:'red'}}`中有两个括号，可能会让人看着发晕，其实它与下面是等价的

```
var styleObj = {color: 'red'};
style={styleObj}
```

##3. 条件判断的4种写法

现在假设我们要写一个页面，如果属性中有`name`，就输出`Hello, name`，如果`name`为空，就输出`Hello, world`。

###3.1 三元表达式

类似C语言的三元表达式。

```
<script type="text/jsx">
var Hello = React.createClass({
    render: function(){
        return <div>Hello, {this.props.name ? this.props.name: "world"}</div>;
    }
});
React.render(<Hello name="liulx"/>, 
    document.getElementById('container'));
</script>
```

###3.2 使用变量

```
<script type="text/jsx">
var Hello = React.createClass({
    getName: function(){
        if (this.props.name) {
            return this.props.name;
        } else{
            return "world"
        };
    },
    render: function(){
        var name = this.getName();
        return <div>Hello, {name}</div>;
    }
});
React.render(<Hello name="liulx"/>, 
    document.getElementById('container'));
</script>
```

###3.3 直接调用函数

这个跟第二种方法类似，就是把大括号去掉。

```
<script type="text/jsx">
var Hello = React.createClass({
    getName: function(){
        if (this.props.name) {
            return this.props.name;
        } else{
            return "world"
        };
    },
    render: function(){
        return <div>Hello, {this.getName()}</div>;
    }
});
React.render(<Hello name="liulx"/>, 
    document.getElementById('container'));
</script>
```

###3.4 使用比较运算符

```
<script type="text/jsx">
var Hello = React.createClass({
    render: function(){
        return <div>Hello, {this.props.name || "World"}</div>;
    }
});
React.render(<Hello name="liulx"/>, 
    document.getElementById('container'));
</script>
```

##4. 函数表达式

```
<script type="text/jsx">
var Hello = React.createClass({
    render: function(){
        return <div>Hello, {
            (function(obj){
                if (obj.props.name) {
                    return obj.props.name;
                } else {
                    return "world";
                }
            })(this)
        }</div>;
    }
});
React.render(<Hello name="liulx"/>, 
    document.getElementById('container'));
</script>
```

