title:Flask笔记－01 基本应用框架
date:2014-10-18 23:11
category:Python
tags:Flask, Flask笔记
author:刘理想

##1. 初始化

```python
from flask import Flask

app = Flask(__name__)
```

`__name__`的作用是用来给Flask来判断应用的根路径的。

##2. 路由和视图函数

```python
@app.route('/')
def hello_world():
    return 'Hello World!'
```

如果要给路由添加参数，可以这样:

```python

```

其中`<name>`就是参数，如果你想给参数加上类型，可以使用`/user/<int:id>`，Flask支持在路由中使用`int`, `float`和`path`等类型。`path`是字符串，但是并不把`/`当作分隔符，而是把它看作动态组成的一部分。

##3. 启动服务器

Flask包含了一个小型的开发服务器，启动它：

```python
if __name__ == '__main__':
    app.run(debug=True)
```

其中`debug=True`用来开启debug模式。正式环境中不需要。

##4. 一个完整的应用

上面我们分别介绍了一部分，现在看看一个整体应用，保存到文件hello.py中：

```python
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(debug=True)
```

启动应用：

```
$python hello.py
 * Running on http://127.0.0.1:5000/
 * Restarting with reloader
```

##5. 请求响应循环

###5.1 应用和请求上下文

Flask使用`上下文Context`的概念来让一些对象暂时性的全局可访问。因为有了上下文，所以可以这样写：

```python
from flask import request

@app.route('/browser')
def browser():
    user_agent = request.headers.get('User-Agent')
    return '<p>你的浏览器是：%s' % user_agent
```

注意`request`是如何被使用的。考虑到并发型，我们知道`request`不能是全局性的变量，所以每个线程都需要一个不同的`request`对象。上下文帮助我们实现了一些变量能在一个线程内全局访问。

Flask有两种上下文， *应用上下文*和*请求上下文*，如下表所示:

变量名称|上下文|描述
-------|-----|----
`current_app`|应用上下文|活动应用的实例对象
`g`|应用上下文|一个对象，应用可以用它在处理请求的过程中来进行暂时存储。每次请求都会被重置。
`request`|应用上下文|请求对象
`session`|请求上下文|用户会话，一个用来在请求之间存储数据的字典。

Flask在分发请求之前激活（或者叫*推送*）应用和请求上下文，并在处理请求后移除它们。当推送应用上下文时，`current_app`和`g`可用，否则会抛出异常。`request`和`session`也是如此。

下面展示了应用上下文如何工作的。

```
>>> from hello import app
>>> from flask import current_app
>>> current_app.name
Traceback (most recent call last):
...
RuntimeError: working outside of application context
>>> app_ctx = app.app_context()
>>> app_ctx.push()
>>> current_app.name
'hello'
>>> app_ctx.pop()
```

注意，使用`app.app_context()`来获得应用上下文。

###5.2 请求分发

Flask使用*URL映射*来进行请求分发。创建映射可以使用`app.route()`装饰器或者`app.add_url_rule()`。

要查看Flask应用中的URL映射，可以这样

```python
>>> from flask_web_dev import app
>>> app.url_map
Map([<Rule '/' (GET, HEAD, OPTIONS) -> hello_world>,
 <Rule '/static/<filename>' (GET, HEAD, OPTIONS) -> static>,
 <Rule '/user/<name>' (GET, HEAD, OPTIONS) -> user>])
```

其中`GET, HEAD, OPTIONS`时方法。`/static/<filename>`是Flask用来做静态文件映射的特殊方法。

###5.3 请求勾子

有时需要在请求执行前和执行后运行一些代码，比如应用启动时创建数据库连接，或者验证发出请求的用户。我们可以使用请求勾子：

- `before_first_request`：注册一个在第一个请求前被运行的方法
- `before_request`: 注册一个在每个请求前运行的方法
- `after_request`: 注册一个在请求后执行的方法，但是当请求发出异常时不执行
- `teardown_request`: 注册一个在请求后执行的方法，即使请求发出异常后仍然执行

一种常见的模式是用全局变量`g`在请求勾子方法和视图方法之间共享数据，比如，`before_request`处理方法用来从数据库中加载用户，并且存储到`g.user`中。在视图方法中可以使用它。

###5.4 响应

Flask期望视图函数返回一个值作为响应给请求。

如果希望返回状态码：

```python
@app.route('/')
def hello_world():
    return 'Hello World!', 400
```

Flask的视图函数除了返回tuple之外还可以直接返回`Response`对象。使用`make_response`可以创建`Response`对象。你可以在这个`Response`对象的基础上再进行配置。

```python
from flask import make_response

@app.route('/')
def index():
    response = make_response('<h1>This document carries a cookie!</h1>')
    response.set_cookie('answer', '42')
    return response
```

还有一个特殊的响应类型叫做*重定向*。它返回一个url给浏览器用来进行跳转，状态码302, url放在返回头的`Location`中。Flask提供了`redirect`方法用来重定向：

```python
@app.route('/redirect_demo')
def redirect_demo():
    return redirect('http://www.baidu.com')
```

另外一个特殊的响应是使用`abort`来处理错误的，不需返回，而是通过引发异常来将控制权转让给web服务器：

```python
from flask import abort


@app.route('/abort_demo/<int:id>')
def abort_demo(id):
    if id != 2:
        abort(404)
    return '<h1>user id is %s' % id
```

##6. Flask扩展

Flask被设计的易于扩展，它有意避开了一些重要领域比如用户认证和数据库，留给用户来自由实现。可以使用扩展或者python包来实现不同的想法。

下面将使用`Flask-Script`来使flask应用可以接受命令行参数。

###6.1 使用Flask-Script来实现命令行参数。

Flask本身实现了一些可配置项，但要在`app.run()`中指定，这不方便。Flask-Script就是添加命令行解析用的。

```
pip install flask-script
```

```python
from flask.ext.script import Manager

manager = Manager(app)

if __name__ == '__main__':
    manager.run()
```

Flask的扩展都在`flask.ext`包下。flask-script中的`Manager`接受Flask的实例对象，这也是Flask扩展的常用方法。

此时，再运行程序，就会有这样的结果：

```
$ python flask_web_dev.py
usage: flask_web_dev.py [-?] {runserver,shell} ...

positional arguments:
  {runserver,shell}
    runserver        Runs the Flask development server i.e. app.run()
    shell            Runs a Python shell inside Flask application context.

optional arguments:
  -?, --help         show this help message and exit
```

- `shell`参数用来再当前应用上下文下启动python，可以用来做测试时使用。
- `runserver`可以使用debug模式启动服务器，但还有更多用法`python flask_web_dev.py runserver -?`

```
$ python flask_web_dev.py runserver -?
usage: flask_web_dev.py runserver [-?] [-h HOST] [-p PORT] [--threaded]
                                  [--processes PROCESSES]
                                  [--passthrough-errors] [-d] [-D] [-r] [-R]

Runs the Flask development server i.e. app.run()

optional arguments:
  -?, --help            show this help message and exit
  -h HOST, --host HOST
  -p PORT, --port PORT
  --threaded
  --processes PROCESSES
  --passthrough-errors
  -d, --debug           enable the Werkzeug debugger (DO NOT use in production
                        code)
  -D, --no-debug        disable the Werkzeug debugger
  -r, --reload          monitor Python files for changes (not 100{'type':
                        None, 'prog': 'flask_web_dev.py runserver',
                        'required': False, 'const': True, 'default': None,
                        'container': <argparse._ArgumentGroup object at
                        0x102aaef28>, 'help': 'monitor Python files for
                        changes (not 100% safe for production use)', 'dest':
                        'use_reloader', 'choices': None, 'option_strings':
                        ['-r', '--reload'], 'metavar': None, 'nargs': 0}afe
                        for production use)
  -R, --no-reload       do not monitor Python files for changes
```

`--host`很有用，它可以用来指定地址。默认情况下Flask使用`localhost`，但是可以这样指定：

```
$python flask_web_dev.py runserver --host 0.0.0.0
* Running on http://0.0.0.0:5000/
```

下一章我们将介绍模板。



