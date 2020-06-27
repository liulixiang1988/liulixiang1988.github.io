Title: Java Web之JSP-03 JSP内置对象
Date: 2015-04-09 16:47
Category: Java
Tags: Java, Web, JSP
Author: 刘理想

##1. jsp内置对象简介

1、JSP内置对象是Web容器创建的一组对象，【不使用new关键字】就可以使用的内置对象。
2、九大内置对象:
out,request,response,session,application（五大常用对象）
Page,pageContext,exception.config（四个不太常用对象）

##2. out对象

缓冲区Buffer：就是内存的一块区域涌来保存临时数据。

out是JspWriter 的实例,是向客户端输出内容的常用对象.
常用方法:

1. void println() 向客户端打印字符串.
2. void clear() 清除缓冲区,在flush之后调用会抛出异常.
3. void clearBuffer() 清除缓冲区,在flush之后调用不会抛出异常.
4. void flush() 将缓冲区内容输出到客户端.
5. int getBufferSize() 
6. int getRemaining()
7. boolean isAutoFlush() 返回缓冲区满时，是自动清空还是抛出异常
8. void close() 关闭输出流

##3. Get和Post提交方式的区别

表单有两种提交方式：get与post。定义在`<form action="dologin.jsp" name="loginForm" method="提交方式***"></form>` 动作／名称等顺序无所谓。
1.get：以【明文】方式，通过URL提交数据，数据在URL中【可以看到】。提交数据最多不超过【2KB】。安全性较低，但效率比post方式高。适合提交数据量不大，且安全要求不高的数据：比如：搜索、查询等功能。
2.post：将用户提交的信息封装在HTML HEADER内。适合提交数据量大，安全性高的用户信息。如：注册、修改、上传等功能。

##4. request对象

客户端的请求信息被封装在request对象中，它是HttpServletRequest类的实例。request对象具有请求域，即完成客户端的请求之前，该对象一直有效。常用方法如下：

1. String getParameter(String name) 返回name制定的参数值
2. String[] getParameterValues(String name) 返回包含参数name的所有值的数组（如复选框的值）。
3. void setAttribute(String, Object) 存储此请求中的属性
4. object getAttribute(String name) 返回制定属性的属性值
5. String getContentType() 得到请求体的mime类型
6. String getProtocol() 返回请求用的协议类型及版本号
7. String getServerName() 返回接受请求的服务器主机名
8. int getServerPort();//返回服务器接受此请求所用的端口号
9. String getCharacterEncoding();//返回字符编码方式
10. void setCharacterEncodinng();//设置请求的字符编码方式
11. int getContentLength();//返回请求体的长途(以字节数)
12. String getRemoteAddr();//返回发送此请求的客户端IP地址,IP地址为IPv6本地环回地址。
13. String getRealPath(String path);//返回虚拟路径的真实路径
14. String request.getContextPath();//返回上下文路径

使用关键：
1. 单个参数的获取使用getParameter(String name), 多个参数的获取getParameterValues(String name)。在使用这两个方法是，需传递对应的参数name，这个name应是在表格中声明的对象。
2. 不存在参数值的方法，直接使用表达式即可获取，例如：`<%=request.getContentType()%>`
3. 传递中文参数时，可能会出现乱码情况，需声明：`request.setCharacterEncoding("utf-8");`但此方法不能解决使用url传递中文时出现的乱码，若想解决这个，需要在server.xml的connector的末尾加上:`URIEncoding="utf-8"`

##5. response对象

response对象包含了响应客户请求的有关信息，但在JSP中很少直接用到它，它是HttpServletResponse类的实例，response对象具有页面作用域，即访问一个页面时，该页面内的response对象只能对这次访问有效，其它页面的response对象对当前页面无效。
常用方法：

1. String getCharacterEncoding();//返回响应的是何种字符编码
2. void setContentType(String type);//设置响应MIME类型
3. PrintWriter getWriter();//返回可以想客户端输出字符的一个对象PrintWriter
4. sendRedirect(java.lang.String location);//重新定向客户端请求

PrintWriter比out对象先打印。调用out.flush()可以解决

###5.1 JSP内置对象——请求转发与请求重定向的区别

1. 请求重定向：服务端responce.sendRedirect("xx.jsp")重定向。【客户端行为】：即客户端会访问两次，第一次访问后会立即跳转到第二个重定向页面上，【从本质上讲等于两次请求】，而前一次的请求封装的request对象不会保存，地址栏的URL地址会改变。
2. 请求转发：服务端request.getRequestDispatcher("xx.jsp").forward(request,response)请求转发。forward(request,response)用于保存内置对象request和response。【服务器行为】：服务器会代替客户端去访问转发页面，【从本质是一次请求】，转发后请求对象会保存，地址栏的URL地址不会改变。

##6. session对象

###6.1 什么是session

1. session表示客户端与服务器的一次会话
2. Web中的session指：用户在浏览某个网站时，从进入网站到浏览器关闭所经过的这段时间，也就是用户浏览网站所花费的时间。
3. 从上述定义中可以看到，session实际是一个【特定的时间概念】
4. 服务器的内存中，保存着不同用户的不同的session。

###6.2 session对象常用方法

1. long getCreationTime() 返回session创建时间
2. public String getId() 返回创建时jsp引擎为它设的唯一id号
3. public Ojbect setAttribute(String name, Object value) 使用指定名称将对象绑定到此对话
4. public Object getAttribute(String name) 返回此会话中的指定名称绑定在一起的对象，如果没有对象绑定在该名称下，则返回null
5. String[] getValueNames() 返回一个包含此session中所有可用属性的数组
6. int getMaxInactiveInterval() 返回两次请求间隔多长时间此session被取消（单位秒）
7. setMaxInactiveInterval(int)可以设置session存活的最长时间，在时间过了之后，服务器会创建全新的一个session。前一个session的内容无法被传递到新的session。

###6.3 session的生命周期

1、创建阶段：当客户端第一次访问某个jsp或者servlet时候，服务器会为当前会话创建一个SessionId，每次客户端向服务端发送请求时，都会将此SessionId携带过去，服务端会对此SessionId进行校验。
2、活动阶段
- 某次会话当中通过超链接打开的新页面属于同一次会话。
- 只要当前会话没有全部关闭，重新打开新的浏览器窗口访问统一项目资源时属于同一次会话。
- 除非本次会话的所有页面【都关闭后】，再重新访问某个Jsp或者Servlet将创建新的会话

**注意：原有会话在超时前会一直存在，只是再没有客户端携带原SessionId交予服务端校验了！**

3、销毁阶段：Session的销毁只有三种方式
- 调用了session.invalidate()方法
- Session过期（超时）
- 服务器重启

4、设置session对象超时时间：默认为30分钟
- session.setMaxInactiveInterval()
- 在web.xml文档配置中,单位是分钟.

```
<session-config>
<session-timeout>2</session-timeout> // 设置为2分钟过期
</session-config>
```

##7. application对象

###7.1 application介绍

1. 实现了用户间数据的共享，可存放全局变量。（类似静态对象）
2. 开始于服务器启动，终止于服务器的关闭（生命周期）
3. 在用户的前后连接或不同用户之间的连接中，可以对application对象的同一属性进行操作
4. 在任何地方对application对象属性进行操作，都将影响到其他用户对此的访问
5. application对象是ServletContext类的实例

###7.2 常用方法如下

application对象常用方法：
1. pblic void setAttribute(String name,Object value);//使用指定名称将对象绑定到此会话
2. public Object getAttribute(String name);//返回与此会话中的指定名称绑定在一起的对象，如果没有对象绑定在该名称下，则返回null.
3. Enumeration getAttributeNames();//返回所有可用属性那个名的枚举
4. String getServerInfo();//返回JSP（SERVER）引擎及版本号

##8. page对象

page对象就是指向当前JSP页面本身，有点像类中的this指针，它是java.lang.Object类的实例。常用方法如下：

- class getClass();   //返回此Object类
- int hashCode(); //返回此Object的hash码
- boolean equals(Object obj); //判断此Object是否与指定的Object对象相等
- void copy(Object obj);  //把此Object复制到指定的Object对象中
- Object clone(); //克隆此Object对象
- String toString();  //把此Object对象转换为String类的对象
- void notify();  //唤醒一个等待的线程
- void notyfyAll();   //唤醒所有等待的线程
- void wait(int timeout); //使一个线程处于等待直到timeout结束或被唤醒
- void wait();    //使一个线程处于等待直到被唤醒

##9. pageContext对象和config对象

###9.1 pageContext对象

- pageContext对象提供了对JSP页面内所有的对象及名字空间的访问
- pageContext对象可以访问到本页所在的session，也可以本页所在的application的某一属性值
- pageContext对象相当于页面中所有功能的集大成者
- pageContext对象的本类名也叫pageContext

常用方法如下：

- JspWriter   getOut();   //返回当前客户端响应被使用的JspWriter流(out)
- HttpSession getSession();   //返回当前页面中HttpSession对象(session)
- Object  getPage();  //返回当前页面的Object对象(Object)
- ServeletRequest getRequest();   //返回当前页面的ServletRequest对象(request)
- ServeletResponse getResponse(); //返回当前页面的ServletResponse对象(response)
- void setAttribute(String name,Object attribute);    //设置属性及属性值
- void getAttribute(String name,int scope);   //在指定范围内取属性的值
- int getAttributeScope(String name); //返回某属性的作用范围
- void forward(String relativeUrlPath);   //使当前页面重导到另一页面，页面URL不会变
- void include(String relativeUrlPath);   //在当前位置包含另一文件

###9.2 Config对象：

config对象是在一个servlet初始化时，JSP引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数(通过属性名和属性值构成)以及服务器的有关信息（通过传递一个ServletContext对象）

常用方法：
1. ServletContext getServletContext();//返回含有服务器相关信息的ServletContext对象
2. String getInitParameter(String name);//返回初始化参数的值
3. Enumeration getInitParameterNames();//返回Servlet初始化所需所有参数的枚举

##10. exception对象

exception对象是一个异常对象，当一个页面在运行过程中规发生了异常，就会产生这个对象。如果一个JSP页面要应用此对象，就必须把`isErrorPage`设为true,否则无法编译。他实际上是java.lang.Throwable的对象。

###10.1 exception对象常用方法

1. String getMessage();//返回描述异常的消息
2. String toString();//返回关于异常的简短描述消息
3. void printStackTrace();//显示异常及其栈轨迹
4. Throwable FillInStackTrace();//重写异常的执行栈轨迹

###10.2 exception对象使用方法：

1. 在可能会抛出异常的页面page指令里，设置errorPage="xxx.jsp"，表示出现异常将抛给xxx页面去处理
2. 在xxx页面里，要使用Exception对象，需要把page指令里的isErrorPage属性设置为true。

##11. 阶段案例-实现用户登录

用户登录页login.jsp

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>login</title>
</head>
<body>
<form action="dologin.jsp" method="post">
  <label for="username">user name</label>
  <input type="text" name="username" placeholder="用户名"/>
  <label for="password">password</label>
  <input type="password" name="password"/>
  <input type="submit" value="login"/>
</form>
</body>
</html>
```

处理用户登录页dologin.jsp

```

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%
  String username = "";
  String password = "";
  request.setCharacterEncoding("utf-8");
  username = request.getParameter("username");
  password = request.getParameter("password");
  if ("admin".equals(username) &&  "admin".equals(password)){
    session.setAttribute("loginUser", username);
    request.getRequestDispatcher("login_success.jsp").forward(request, response);
  }
  else{
    response.sendRedirect("login_failure.jsp");
  }
%>
```

登录成功页login_success.jsp

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title></title>
</head>
<body>
<h1>login success!</h1>
<%
  String loginUser = "";
  if(session.getAttribute("loginUser") != null){
    loginUser = session.getAttribute("loginUser").toString();
  }
%>

<p>欢迎，<%=loginUser%></p>
</body>
</html>
```

登录失败页

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title></title>
</head>
<body>
<h1>login failure.</h1>
<hr/>
<a href="login.jsp">返回登录页</a>
</body>
</html>
```



