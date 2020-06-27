---
title: Java Web之Servlet-02 应用MVC架构实现项目
date: 2015-04-15 19:49
category: Java
tags: Java, Web, Servlet
author: 刘理想
---

## 1. 获取初始化参数

```xml
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

```java
在servlet的init()方法中获取初始化的值：
this.getInitParameter("username");
this.getInitParameter("password");
```

