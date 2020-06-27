Title: Java Web之Servlet-02 应用MVC架构实现项目
Date: 2015-04-15 19:49
Category: Java
Tags: Java, Web, Servlet
Author: 刘理想

##1. 获取初始化参数

```
<servlet>
    <init-param>
        <param-name>username</param-name>
        <param-value>admin</param-value>
    </init-param>
    <init-param>
        <param-name>password</param-name>
        <param-value>123456</param-value>
    </init-param>
</servlet>
```

```
在servlet的init()方法中获取初始化的值：
this.getInitParameter("username");
this.getInitParameter("password");
```

