Title: React使用笔记(5)-React组件的协同使用
Date: 2015-11-03 23:53
Category: Web
Tags: JavaScript,React
Author: 刘理想

[TOC]

##1. 组件协同使用介绍

组件的协同就是对组件的一种**组件、管理的方式**。

目的：

- 逻辑清晰
- 代码模块化
- 封装细节
- 代码可复用

组织方式：

- 组件嵌套：实现封装
- Mixin:直接嵌入代码，实现复用

##2. 组件嵌套

组件嵌套的本质是**父子关系**。

父组件和子组件是如何通信的呢？
父组件给子组件通信是用属性。
子组件对父组件通信是：父组件把事件处理函数作为属性传递给子组件，子组件再进行调用。

```jsx
<script type="text/jsx">
    var Radio = React.createClass({ 
        getInitialState: function () {
            return {
                value: this.props.defaultValue
            }; 
        },
        handleChange: function (event) { 
            if (this.props.onChange) {
                this.props.onChange(event); }
                this.setState({
                value: event.target.value
            }); 
        },
        render: function () {
            var children = {};
            var value = this.props.value || this.state.value;
            React.Children.forEach(this.props.children, function (child, i) {
                var label = <label> 
                    <input type="radio" name={this.props.name} value={child.props.value} checked={child.props.value == value} onChange={this.handleChange} />
                    {child.props.children}
                    <br/>
                </label>;
                children['label' + i] = label; 
            }.bind(this));
            return <span>{children}</span>; 
        }
    });
    var MyForm = React.createClass({
        getInitialState: function () { 
            return {my_radio: "B"};
        },
        handleChange: function (event) {
            this.setState({
                my_radio: event.target.value
            }); 
        },
        submitHandler: function (event) { 
            event.preventDefault(); 
            alert(this.state.my_radio);
        },
        render: function () {
            return <form onSubmit={this.submitHandler}> 
                <Radio name="my_radio" value={this.state.my_radio} onChange={this.handleChange}>
                    <option value="A">First Option</option> 
                    <option value="B">Second Option</option>
                    <option value="C">Third Option</option>
                </Radio>
                <button type="submit">Speak</button>
            </form>;
        } 
    });
    React.render(<MyForm></MyForm>, document.body);
</script>
```


