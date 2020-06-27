Title: JavaScript模块化
Date: 2015-11-04 13:10
Category: Web
Tags: JavaScript
Author: 刘理想

[TOC]

##1. 模块化解读

AMD是RequireJS在推广过程中对模块定义的规范化产出。

- 异步加载模块，依赖前置，提前执行
- Define定义模块 `define(['require', 'foo'], function(){return;});`
- Require加载模块（依赖前置）`require(['foo', 'bar'], function(foo, bar){});`

CMD是SeaJS在推广过程中对模块定义的规范化产出。

- Define定义exports导出`define(function(require, exports, module){});` module上存储了当前模块的一些对象。
- require(./a)直接引。`Require.async`异步引入.
- 同步加载，依赖就近，延迟执行。

类似的还有CommonJS Module/2.0规范。

##2. SeaJS的应用

目录结构：

```
examples/
  |-- sea-modules      存放 seajs、jquery 等文件，这也是模块的部署目录
  |-- static           存放各个项目的 js、css 文件
  |     |-- hello
  |     |-- lucky
  |     `-- todo
  `-- app              存放 html 等文件
        |-- hello.html
        |-- lucky.html
        `-- todo.html
```

我们从 `hello.html` 入手，来瞧瞧使用 Sea.js 如何组织代码。

###2.1 在页面中加载模块

在 `hello.html` 页尾，通过 `script` 引入 `sea.js` 后，有一段配置代码：

```
// seajs 的简单配置
seajs.config({
  base: "../sea-modules/",
  alias: {
    "jquery": "jquery/jquery/1.10.1/jquery.js"
  }
})

// 加载入口模块
seajs.use("../static/hello/src/main")
```

`sea.js` 在下载完成后，会自动加载入口模块。页面中的代码就这么简单。

###2.2 模块代码

这个小游戏有两个模块 `spinning.js` 和 `main.js`，遵循统一的写法：

```
// 所有模块都通过 define 来定义
define(function(require, exports, module) {

  // 通过 require 引入依赖
  var $ = require('jquery');
  var Spinning = require('./spinning');

  // 通过 exports 对外提供接口
  exports.doSomething = ...

  // 或者通过 module.exports 提供整个接口
  module.exports = ...

});
```

上面就是 `Sea.js` 推荐的 CMD 模块书写格式。如果你有使用过 Node.js，一切都很自然。

