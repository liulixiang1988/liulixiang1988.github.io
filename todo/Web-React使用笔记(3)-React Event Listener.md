Title: React使用笔记(3)-React Event Listener
Date: 2015-11-28 12:18
Category: Web
Tags: JavaScript,React
Author: 刘理想

[TOC]

##1. 构造基本结构

首先，我们先创建一个按钮，一个输入框。我们准备点击按钮时，后面有一个`<span>`显示和隐藏。代码如下：

```jsx
<div id="container"></div>
<script type="text/jsx">
    var TestClickComponent = React.createClass({
        render: function(){
            return (
                <div>
                    <button>显示|隐藏</button>
                    <span>测试点击</span>
                </div>
                );
        }
    });

    var TestInputComponent = React.createClass({
        getInitialState: function(){
            return {
                inputContent: ''
            }
        },
        render: function(){
            return (
                <div>
                    <input type="text" /><span>{this.state.inputContent}</span>
                </div>
                );
        }
    });

    React.render(
        <div>
            <TestClickComponent />
            <TestInputComponent />
        </div>,
        document.getElementById('container'));
</script>
```

这里有几点需要注意，在`render`里返回的时候，需要用个`<div>`包裹起来，因为每个里面有好几html标签。

##2. 给`<button>`添加事件绑定

用驼峰式命名的方式来绑定事件，比如`onClick`。注意这里的`onClick`事件和原生HTML属性中的`onclick`不是一回事儿。这里，它并不是一个真事的DOM节点，它只是一个React Element，而且写法也不一样，HTML标签的属性对于大小写是不敏感的，而React Element对于大小写是敏感的。

`onClick`的处理函数一般通过对象属性封装在React组件的对象实例上。

```jsx
var TestClickComponent = React.createClass({
    handleClick: function(event){
        
    },

    render: function(){
        return (
            <div>
                <button onClick={this.handleClick}>显示|隐藏</button>
                <span>测试点击</span>
            </div>
            );
    }
});
```

`handleClick`函数的参数是React封装的`event`对象。这个`event`对象是在原生的js的`event`对象的基础上封装的，因此，我们可以调用一些在原生js的`event`对象上的方法：

```js
handleClick: function(event){
    event.stopPropagation();
    event.preventDefault();
}
```

我们如何在`event`事件处理函数内处理`<span>`呢，我们通过给`<span>`添加一个`ref`属性，然后就能在`this.refs`中获取这个对象了。

```jsx
<span ref="tip">测试点击</span>
```

注意，我们使用`this.refs.tip`引用的并不是真实的DOM节点，而是React组件。我们要操作DOM节点该怎么办呢？使用React给我们封装的方法`React.findDOMNode`，参数就是我们的React组件。

```jsx
handleClick: function(event){
    var tipE = React.findDOMNode(this.refs.tip)

    if (tipE.style.display === 'none'){
        tipE.style.display = 'inline';
    } else {
        tipE.style.display = 'none';
    }
    event.stopPropagation();
    event.preventDefault();
}
```

##3. 给`<input>`添加事件绑定

我们给`<input>`添加`onChange`事件，这里我们使用`event.target.value`来获取`<input>`的值，然后设置`state`，对应的`<span>`的内容就会跟着改变。

```jsx
changeHandler: function(event){
    this.setState({
        inputContent: event.target.value
    });
    event.preventDefault();
    event.stopPropagation();
},
render: function(){
    return (
        <div>
            <input type="text" onChange={this.changeHandler}/><span>{this.state.inputContent}</span>
        </div>
        );
}
```

##4. 非DOM属性

非DOM属性 
- `dangerouslySetInnerHTML`: 在JSX中直接插入HTML代码；
- `ref`:前面已经提过，父组件引用子组件；
- `key`:提高渲染性能。

##5. 事件列表

###5.1 触摸事件

只在移动端可用

- `onTouchCancel`
- `onTouchEnd`
- `onTOuchMove`
- `onTouchStart`

###5.2 键盘类事件

- `onKeyDown`
- `onKeyPress`:`onKeyDown`和`onKeyUp`的组合
- `onKeyUp`

###5.3 剪切类事件

目前支持的不是特别好，使用的不多。

- `onCopy`
- `onCut`
- `onPaste`

###5.4 表单类事件

- `onChange`:输入框、单选框、下拉列表
- `onInput`:文字输入
- `onSubmit`:表单提交，通过这个事件可以禁用默认跳转

###5.5 焦点事件

- `onFocus`
- `onBlur`

###5.6 UI元素事件

- `onScroll`:UI元素的滚动事件

###5.7 滚动事件

- `onWheel`: 鼠标滚动事件，可以监听鼠标的滚动幅度，滚动方位

###5.8 鼠标事件

- `onClick`
- `onContextMenu`:右键，上下文菜单
- `onDoubleClick`
- `onMouseDown`
- `onMouseEnter`
- `onMouseLeave`
- `onMouseMove`
- `onMouseOut`
- `onMouseOver`
- `onMouseUp`

另外拖拽事件

- `onDrop`
- `onDrag`
- `onDragEnd`
- `onDragEnter`
- `onDragExit`
- `onDragLeave`
- `onDragOver`
- `onDragStart`

##6. 事件对象介绍

前面我们介绍过，事件对象是对原生事件对象的封装，下面我们来看看事件对象的一些属性。

###6.1 通用属性

- `boolean bubbles`:是否可以冒泡
- `boolean cancelable`:是否可以取消事件
- `DOMEventTarget currentTarget`
- `boolean defaultPrevented`:事件是否禁止了默认行为
- `number eventPhase`: 事件所处的阶段
- `boolean isTrusted`: 事件是否可信，用户的事件可信，JS代码触发的事件不可信
- `DOMEvent nativeEvent`: 原生浏览器事件，没经过
- `void preventDefault()`: 禁止默认行为
- `void stopPropagation()`: 禁止冒泡
- `DOMEventTarget target`:
- `number timeStamp`: 事件的时间
- `string type`: 事件类型

###6.2 特有属性

剪切事件：

- `DOMDataTransfer clipboardData`: 剪切的一些数据

键盘事件：

- `boolean altKey`:
- `Number charCode`:字符编码（ABCD)
- `boolean ctrlKey`
- `function getModifierState(key)`:辅助案件(ctrl shift等)
- `String key`
- `Number keyCode`:按键编码
- `String locale`
- `Number location`:位置
- `boolean metaKey`:win和cmd
- `boolean repeat`:是否重复
- `boolean shiftKey`
- `Number which`:`charCode`和`keyCode`的统一

焦点事件：

- `DOMEventTarget relatedTarget`: AB控件切换时，他们互为`relatedTarget`

鼠标事件：

- `boolean altKey`
- `Number button`
- `Number buttons`
- `Number clientX`:浏览器窗框的左上角是(0,0)
- `Number clientY`
- `boolean ctrlKey`
- `function getModifierState(key)`
- `boolean metaKey`
- `Number pageX`:HTML页面的左上角(0,0)
- `Number pageY`
- `DOMEventTarget relatedTarget`
- `Number screenX`:显示器的左上角
- `Number screenY`
- `boolean shiftKey`

触摸事件：

- `boolean altKey`
- `boolean ctrlKey`
- `function getModifierState(key)`
- `boolean metaKey`
- `boolean shiftKey`
- `DOMTouchList changedTouches`:`DOMTouchList`触摸事件特有类型
- `DOMTouchList targetTouches`:
- `DOMTouchList touches`:

UI元素：

- `Number detail`:滚动的距离
- `DOMAbstractView view`

鼠标滚动：

- `Number deltaMode`：单位
- `Number deltaX`：x,y,z轴移动距离
- `Number deltaY`
- `Number deltaZ`

##7. 实例演练

###7.1 使用滚动事件对象

鼠标滚轮的数值可以当做RGB值使用

```jsx
<script type="text/jsx">
var Hello = React.createClass({
    getInitialState: function () {
        return {
            background: '#FFFFFF'
        };
    },
    handleWheel: function (event) {
        //this.state.background.substr(1)：忽略掉第一个"#"
        //deltaY:是+3或者-3
        var newColor = (parseInt(this.state.background.substr(1), 16) + 
            event.deltaY * 977).toString(16);
        newColor = '#' + newColor.substr(newColor.length-6).toUpperCase();
        this.setState({background: newColor});
    },
    render: function(){
        console.log(this.state);
        return <div onWheel={this.handleWheel} style={this.state}><p>Hello world</p></div>;
    }
});
React.render(<Hello/>, 
    document.getElementById('container'));
</script>
```

###7.2 键盘事件

```jsx
var Hello = React.createClass({
    getInitialState: function () {
        return {
            password: ''
        };
    },
    handleKeyPress: function (event) {
        this.setState({password: this.state.password+event.which});
        console.log(this.state);
    },
    handleChange: function(event){
        event.target.value = '';
    },
    render: function(){
        console.log(this.state);
        return <div>
        <input type="text" onKeyPress={this.handleKeyPress} onChange={this.handleChange} />
        <p style={{'display': this.state.password.indexOf('495051') >= '0' ? 'inline': 'none'}}>you got it!</p>
        </div>;
    }
});
```

##8. 事件和状态关联

```jsx
handleChange: function(event){
    this.setState({inputText: event.target.value});
}
```

作者：liulixiang1988#gmail.com (#换成@)
参考链接：http://www.imooc.com/learn/504