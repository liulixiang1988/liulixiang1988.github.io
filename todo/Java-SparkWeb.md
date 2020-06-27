Title: 使用Spark进行Java Web开发
Date: 2016-05-17 21:16
Category: Java
Tags: Java
Author: 刘理想

[TOC]

## 1.搭建Spark项目

使用Idea创建Gradle项目，包名是：`io.github.liulixiang.course`,Artifactid是`course-ideas`。
在builder.gradle里添加spark依赖。

```
compile "com.sparkjava:spark-core:2.5"
compile "org.slf4j:slf4j-simple:1.7.21"
```

然后创建`io.github.liulixiang.course.Main`类。

```java
import static spark.Spark.get;
public class Main {
    public static void main(String[] args) {
        get("/hello", (req, res) -> "Hello World");
        get("/", (req, res) -> "欢迎");
    }
}
```

此时，访问http://localhost:4567 就能看到返回的页面了。


## 2.返回响应

Spark支持多种模板引擎，我们使用Handlebars。

添加依赖：
```
compile "com.sparkjava:spark-template-handlebars:2.3"
```

为了让IDEA支持Handlebars，我们在plugin上搜索Handlebars。关于handlebars的使用，可以参考https://github.com/perwendel/spark-template-engines/tree/master/spark-template-handlebars

现在在resources下面新建templates文件夹。然后在文件夹内新建`index.hbs`文件：

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>同学们好!</title>
</head>
<body>
    <h1>同学们好!</h1>
</body>
</html>
```

修改Main:

```java
get("/", (req, res) -> new ModelAndView(null, "index.hbs"), new HandlebarsTemplateEngine());
```

`ModelAndView`表示第一个参数是Model，第二个参数是View。

## 3.接受请求

要获取提交的数据，使用`req.queryParams`方法。

```java
post("/sign-in", (req, res) -> {
    Map<String, String> model = new HashMap<>();
    model.put("username", req.queryParams("username"));
    return new ModelAndView(model, "sign-in.hbs");
}, new HandlebarsTemplateEngine());
```

添加`sign-in.hbs`：
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录</title>
</head>
<body>
    <h1>你好, {{username}}</h1>
</body>
</html>
```

## 4.Cookies

**写入cookie：**
cookie使用res设置：

```java
String username = req.queryParams("username");
res.cookie("username", username);
```

**读取cookie**

```java
get("/", (req, res) -> {
    Map<String, String> model = new HashMap<>();
    model.put("username", req.cookie("username"));
    return new ModelAndView(model, "index.hbs");
}, new HandlebarsTemplateEngine());
```

## 5.创建Model

## 7.Template复用和静态资源处理

## 8. 添加并遍历List

## 9. 重定向

## 10. 使用Filter和请求属性

## 11. 存取指定项目

## 12. 细节

## 13. 消息

## 14. 创建flash message

## 15. 请求处理



