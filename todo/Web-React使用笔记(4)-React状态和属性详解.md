Title: React使用笔记(4)-React状态和属性详解
Date: 2015-12-03 20:58
Category: Web
Tags: JavaScript,React
Author: 刘理想

[TOC]

##1. 属性的含义和用法

###1.1 属性赋值

`<HelloWorld name="?"><HelloWorld>`中属性的值可以有以下几种形式：

- `"Tom"` 字符串
- `{123}` 字符
- `{"Tom"}` 等同于第一种方法
- `{[1, 2, 3]}` 数组，属性接收的也是数组，这样就能做一些类似`checkbox`的事情了
- `{variable or function}` 对象或者函数

还有一种写法，叫做**展开语法**，用3个点加对象名来实现：

```jsx
var props = {
    one: "123",
    two: 321
};
<Hello {...props}/>
```

还有一种不常用的方法`setProps`:

```jsx
var instance = React.render(<HelloWorld></HelloWorld>, document.body);
instance.setProps({name:"Tom"});
```

##2. 状态的含义和用法

- `getInitialState`: 初始化每个实例特有的状态。
- `setState`: 更新组件状态

##3. 属性和状态的对比

--|属性|状态
--|--|--
能否从父组件获取初始值|能|否
能否由父组件修改|能|否
能否在组件内部设置默认值|能|能
能否在组件内部修改|否|能
能否设置子组件的初始值|能|否
能否修改子组件的值|能|否

记住一点：组件在运行时，**需要修改**的数据就是状态。

##4. 实战：文章评论框

首先我们先写个框架：

```jsx
<script type="text/jsx">
var Content = React.createClass({
    render: function(){
        return <p>content</p>;
    },
});

var Comment = React.createClass({
    render: function(){
        return <Content></Content>
    },
});
React.render(<Content ></Content>, document.body);
</script>
```

然后我们动态设置一个下拉框：

```jsx
var Comment = React.createClass({
    getInitialState: function(){
        return {
            name: ["Tim", "Hank", "John"]
        };
    },
    render: function(){
        var options = [];
        for (var i = 0; i < this.state.name.length; i++) {
            options.push(<option value={i}>{this.state.name[i]}</option>);
        };
        return <div>
            <select>
                {options}
            </select>
            <Content></Content>
        </div>;
    },
});
```

为了获取被选中的值，我们添加一个`selectName`的`state`，并且在select下拉选择改变时，设置`selectName`，并且将这个值传递给`Comment`：

```jsx
var Content = React.createClass({
    render: function(){
        return <p>{this.props.selectName}</p>;
    },
});

var Comment = React.createClass({
    getInitialState: function(){
        return {
            name: ["Tim", "Hank", "John"],
            selectName: ''
        };
    },
    handleSelect: function(event){
        this.setState({selectName: event.target.value});
    },
    render: function(){
        var options = [];
        for (var i = 0; i < this.state.name.length; i++) {
            options.push(<option value={i}>{this.state.name[i]}</option>);
        };
        return <div>
            <select onChange={this.handleSelect}>
                {options}
            </select>
            <Content selectName={this.state.selectName}></Content>
        </div>;
    },
});
```

然后我们给`Comment`添加一个`<textarea>`，用来输入评论。

```jsx
var Content = React.createClass({
    getInitialState: function () {
        return {
            text: "reply to: "+ this.props.selectName,
        };
    },
    render: function(){
        return <div>
            <textarea placeholder="please enter something"></textarea>
        </div>;
    },
});
```

下一步，我们监听文本框的变化，并把变化记录在`state`中。

```jsx
var Content = React.createClass({
    getInitialState: function () {
        return {
            inputText: ""
        };
    },
    handleChange: function (event) {
        this.setState({inputText: event.target.value});
    },
    handleSubmit: function (event) {
        console.log("reply to: "+ this.props.selectName+"\n"+this.state.inputText);  
    },
    render: function(){
        return <div>
            <textarea onChange={this.handleChange} placeholder="please enter something"></textarea>
            <button onClick={this.handleSubmit}>submit</button>
        </div>;
    },
});
```

这里有一个bug，就是`getInitialState`只执行一次，当父组件给其传递值之后，这个函数不会再被调用，这是常见的一个问题，即把需要计算的属性放在`getInitialState`中，常见的做法是把需要计算的动作放在最后。

我们对代码进行修改， 我们移除状态，并直接再需要用到属性的地方直接引用。

```jsx
getInitialState: function () {
    return {
        inputText: ""
    };
},
handleSubmit: function (event) {
    console.log("reply to: "+ this.props.selectName+"\n"+this.state.inputText);  
},
```

我们再说一遍，把真正需要计算的值放在需要用到的地方。