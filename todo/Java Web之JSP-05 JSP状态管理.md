Title: Java Web之JSP-05 JSP状态管理
Date: 2015-04-10 16:30
Category: Java
Tags: Java, Web, JSP
Author: 刘理想

##1. http协议的无状态性

1. 无状态是指，当浏览器发送请求给服务器的时候，服务器会响应。但当同一个浏览器再次发送请求时，服务器不会知道是刚才那个浏览器。
2. 简单说，服务器【不会保存用户状态】，不会记得客户端是否访问过，所以这就是无状态协议。

##2. Cookie概述

保存用户的状态的两大机制：session ，cookie

cookie作用:

1. 对特定对象的追踪 
2. 保存用户网页浏览记录与习惯
3. 简化登录

安全风险：容易泄露用户信息
典型应用一：判断注册用户是否已经登录网站
典型应用二：“购物车”的处理

##3. JSP页面中创建和使用Cookie

1. 创建Cookie对象：`Cookie cookie = new Cookie(String key , Object value);`
2. 写入Cookie对象：`response.addCookie(cookie);`
3. 读取Cookie对象：`Cookie[] cookies = request.getCookies();`

Cookie常用方法：

1. `void setMaxAge(int expiry)` 设置cookie的有效期，以秒为单位
2. `void setValue(String value)` 在cookie创建后，对cookie进行赋值
3. `String getValue()` 获取cookie的值
4. `int getMaxAge()` 获取cookie的有效时间，以秒为单位

##4. 案例：Cookie在登录中的应用

java.net包中的URLEncoder和URLDecoder可以用来编码和解码，避免出现中文乱码问题
`String username = URLEncoder.encode(request.getParameter("username"),"utf-8");//编码，防止中文乱码`

login.jsp

```
<%@ page import="java.net.URLDecoder" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>cookie登录</title>
  </head>
  <body>
  <%
    request.setCharacterEncoding("utf-8");
    String username = "";
    String password = "";
    Cookie[] cookies = request.getCookies();
    if(cookies != null && cookies.length > 0){
      for(Cookie c : cookies){
        if(c.getName().equals("username")){
          username = URLDecoder.decode(c.getValue(), "utf-8") ;
        }
        if(c.getName().equals("password")){
          password = URLDecoder.decode(c.getValue(), "utf-8") ;
        }
      }
    }
  %>
  <form action="dologin.jsp" method="post">
    <input type="text" name="username" placeholder="用户名" value="<%=username%>"/><br/>
    <input type="password" name="password" placeholder="密码" value="<%=password%>"/><br/>
    <input type="checkbox" name="isUseCookie" checked="checked"/>记住我
    <input type="submit" value="登录"/>
  </form>
  </body>
</html>
```

dologin.jsp
```
<%@ page import="java.net.URLEncoder" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>登录</title>
</head>
<body>
<h1>登录成功</h1>
<hr/>
<%
  request.setCharacterEncoding("utf-8");
  String[] isUseCookies = request.getParameterValues("isUseCookie");
  if (isUseCookies != null && isUseCookies.length > 0){
    //保存cookie
    String username = URLEncoder.encode(request.getParameter("username"), "utf-8") ;
    String password = URLEncoder.encode(request.getParameter("password"), "utf-8");
    Cookie usernameCookie = new Cookie("username", username);
    Cookie passwordCookie = new Cookie("password", password);
    usernameCookie.setMaxAge(864000); //10天过期
    passwordCookie.setMaxAge(864000);
    response.addCookie(usernameCookie);
    response.addCookie(passwordCookie);
  }else{
    //清楚cookie
    Cookie[] cookies = request.getCookies();
    if(cookies != null && cookies.length > 0){
      for(Cookie c : cookies){
        if(c.getName().equals("username") || c.getName().equals("password")){
          c.setMaxAge(0);
          response.addCookie(c); //设置好cookie后还要添加进去
        }
      }
    }
  }
%>

<a href="user.jsp">查看用户</a>
</body>
</html>
```

user.jsp
```
<%@ page import="java.net.URLDecoder" %>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>用户信息</title>
</head>
<body>
<%
  request.setCharacterEncoding("utf-8");
  String username = "";
  String password = "";
  Cookie[] cookies = request.getCookies();
  if(cookies != null && cookies.length > 0){
    for(Cookie c : cookies){
      if(c.getName().equals("username")){
        username = URLDecoder.decode(c.getValue(), "utf-8") ;
      }
      if(c.getName().equals("password")){
        password = URLDecoder.decode(c.getValue(), "utf-8") ;
      }
    }
  }
%>
<input type="text" name="username" placeholder="用户名" value="<%=username%>"/><br/>
<input type="password" name="password" placeholder="密码" value="<%=password%>"/><br/>
</body>
</html>
```