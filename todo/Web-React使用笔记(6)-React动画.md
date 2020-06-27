Title: React使用笔记(6)-React动画
Date: 2015-11-04 10:09
Category: Web
Tags: JavaScript,React
Author: 刘理想

[TOC]

##1. CSS3动画

```jsx
<head>
    <meta charset="UTF-8">
    <title>React动画</title>
    <style>
    .example-enter {
      opacity: 0.01;
      transition: opacity .5s ease-in;
    }

    .example-enter.example-enter-active {
      opacity: 1;
    }

    .example-leave {
      opacity: 1;
      transition: opacity .5s ease-in;
    }

    .example-leave.example-leave-active {
      opacity: 0.01;
    }
    </style>
</head>
<body>
    <script src="./react-0.13.2/react-0.13.2/build/react-with-addons.js"></script>
    <script src="./react-0.13.2/react-0.13.2/build/JSXTransformer.js"></script>
    <script type="text/jsx">
        var ReactCSSTransitionGroup = React.addons.CSSTransitionGroup;

        var TodoList = React.createClass({
          getInitialState: function() {
            return {items: ['hello', 'world', 'click', 'me']};
          },
          handleAdd: function() {
            var newItems =
              this.state.items.concat([prompt('Enter some text')]);
            this.setState({items: newItems});
          },
          handleRemove: function(i) {
            var newItems = this.state.items;
            newItems.splice(i, 1);
            this.setState({items: newItems});
          },
          render: function() {
            var items = this.state.items.map(function(item, i) {
              return (
                <div key={item} onClick={this.handleRemove.bind(this, i)}>
                  {item}
                </div>
              );
            }.bind(this));
            //这里需要注意transitionName必须和样式名字头一致
            return (
              <div>
                <button onClick={this.handleAdd}>Add Item</button>
                <ReactCSSTransitionGroup transitionName="example">
                  {items}
                </ReactCSSTransitionGroup>
              </div>
            );
          }
        });
        React.render(<TodoList></TodoList>, document.body);
    </script>
</body>
```

##2. JS动画

```jsx
<script src="./react-0.13.2/react-0.13.2/build/react.js"></script>
<script src="./react-0.13.2/react-0.13.2/build/JSXTransformer.js"></script>
<script type="text/jsx">
    var Positioner = React.createClass({
        getInitialState: function() {
            return {
                position: 0
            };
        },
        resolveSetTimeout: function() {
            if (this.state.position < this.props.position) {
                this.setState({
                    position: this.state.position + 1
                });
            }
        },
        componentDidUpdate: function() {
            if (this.props.position) {
                setTimeout(this.resolveSetTimeout, this.props.timeoutMs);
            }
        },
        render: function() {
                var divStyle = {
                    marginLeft: this.state.position
                };
                return <div style={divStyle}> This will animate! </div> 
            } 
        }) 
    React.render(<Positioner></Positioner>, document.body);
    //由于此时界面中已经有了组件，所以React会修改现有组件，就会触发componentDidUpdate
    React.render(<Positioner position={100} timeoutMs={10}></Positioner>, document.body);
</script>
```

##3. rAF动画

```jsx
<script type="text/jsx">
    var Positioner = React.createClass({
        getInitialState: function() {
            return {
                position: 0
            };
        },
        resolveSetTimeout: function() {
            if (this.state.position < this.props.position) {
                this.setState({
                    position: this.state.position + 1
                });
            }
        },
        componentDidUpdate: function() {
            if (this.props.position) {
                requestAnimationFrame(this.resolveSetTimeout);
            }
        },
        render: function() {
                var divStyle = {
                    marginLeft: this.state.position
                };
                return <div style={divStyle}> This will animate! </div> 
            } 
        }) 
    React.render(<Positioner></Positioner>, document.body);
    React.render(<Positioner position={100}></Positioner>, document.body);
</script>
```


