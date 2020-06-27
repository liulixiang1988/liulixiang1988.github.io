Title: React使用笔记(7)-React调优
Date: 2015-11-04 11:25
Category: Web
Tags: JavaScript,React
Author: 刘理想

[TOC]

首先使用`react-with-addons.js`代替`react.js`。

在浏览器控制台使用`React.addons.Perf.start()`来记录操作用时，然后用`React.addons.Perf.stop()`。

查看结果：`React.addons.Perf.printInclusive()`.

```jsx
<script type="text/jsx">
    var NumberDiv = React.createClass({
        getInitialState: function () {
            return {
                number: this.props.number,
            }
        },
        shouldComponentUpdate: function (nextProps) {
            return nextProps.render
        },
        render: function () {
            var style = {
                height: "100px",
                width: "100px",
                float: "left",
                margin: "10px",
                backgroundColor: "gray",
            }
            var chosedStyle = {
                height: "100px",
                width: "100px",
                float: "left",
                margin: "10px",
                backgroundColor: "red",
            }
            return <div style={this.props.chosedNumber === this.state.number ? chosedStyle : style}>
                {this.state.number}
            </div>
        }
    })
    var ChooseNumber = React.createClass({ 
        getInitialState: function () {
            return {
                inputNumber: -1,
                lastNumber: -1
            }
        },
        handleChange: function (e) {
            if (parseInt(e.target.value) !== this.state.inputNumber) {
                this.setState({
                    inputNumber: parseInt(e.target.value),
                    lastNumber: this.state.inputNumber
                })
            }  
        },
        render: function () {
            var inputStyle = {
                display: "block",
                clear: "both",
                width: "200px",
                marginBottom: "10px",
            }
            var children = []
            for (var i = 0; i < 5000; i++) {
                children.push(<NumberDiv key={i} render={i == this.state.inputNumber || i == this.state.lastNumber} chosedNumber={this.state.inputNumber} number={i}></NumberDiv>) 
            }
            return <div> 
                <input type="text" style={inputStyle} placeholder="请输入一个数字" onChange={this.handleChange} />
                {children}
            </div>;
        } 
    });
    React.render(<ChooseNumber></ChooseNumber>, document.body);
</script>
```