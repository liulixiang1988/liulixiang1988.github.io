Title: Java Web之JSP-01 Java Web简介
Date: 2015-04-07 21:40
Category: Java
Tags: Java, Web, JSP
Author: 刘理想


##1. Tomcat配置

###1.1 创建环境变量
创建环境变量`CATALINA_HOME`,值为Tomcat所在根目录（比如，windows下可能为为`C:\Program Files (x86)\Apache Software Foundation\Tomcat 7.0`).

###1.2 启动Tomcat

在`CATALINA_HOME`的bin目录下有startup脚本。

###1.3 测试

地址：http://localhost:8080

##2. Tomcat目录结构

![tomcat](images/java-web-jsp-01.png)

##3. 手工编写第一个Java  Web应用程序

###3.1 创建Web App目录

在Tomcat的webapps目录下创建目录myhome

###3.2 在myhome目录下创建内容

1、创建index.jsp:
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>你好</title>
</head>
<body>
	<h1>你好世界</h1>
</body>
</html>
```

2、创建WEB-INF目录
从Tomcat的webapps/examples/WEB-INF下拷贝web.xml，并且只保留根标签

```
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                      http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
  version="3.0"
  metadata-complete="true">
</web-app>
```

创建classes和lib子文件夹

##4. WEB-INF目录详解

1. WEB-INF目录是一个安全目录，只有通过服务器才能访问。
2. web.xml,项目部署文件，比如配置项目的欢迎页面。
```
	<welcome-file-list>
		<welcome-file>/haha.jsp</welcome-file>
	</welcome-file-list>
```
3. classes文件夹，用以放置*.class文件。
4. lib文件夹，用于存放需要的jar包。

##5. 使用eclipse开发web

###5.1 MyEclipse配置JRE

windows->preference->java->Installed JREs

###5.2 MyEclipse集成Tomcat服务器

windows->preference->Myeclipse->server->Runtime Environments

启动Tomcat

###5.3 使用MyEclipse创建Java web应用

新建web project，命名为MyFirstWebApp，其余默认

###5.4 修改字体

windows->preference->General->Appearance->Colors and Fonts，然后修改Text Font。

##6. 修改Tomcat服务器默认端口

修改conf/server.xml文件

```
<Connector executor="tomcatThreadPool"
           port="8080" protocol="HTTP/1.1"
           connectionTimeout="20000"
           redirectPort="8443" />
```

           
