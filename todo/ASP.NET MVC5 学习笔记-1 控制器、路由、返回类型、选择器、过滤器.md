Title: ASP.NET MVC5 学习笔记-1 控制器、路由、返回类型、选择器、过滤器
Date: 2014-10-31 9:28
Category: DotNet
Tags: DotNet, Web, ASP.NET MVC
Author: 刘理想

#ASP.NET MVC5 学习笔记-1 控制器、路由、返回类型、选择器、过滤器

##1. Action

###1.1 新建项目

新建项目->Web->Asp.net Web应用程序，选择MVC，选择添加测试。

在解决方案上右键，选择"管理NuGet程序包"，在更新页更新全部程序包。

###1.2 控制器

控制器在Controllers文件夹内，命名规则是"名称+Controller"

---

##2. 路由

###2.1 路由规则

```
{controller}/{action}/{id}
```

其中{id}是可选的。

###2.2 路由定义RouteConfig.cs

```
public class RouteConfig
{
    public static void RegisterRoutes(RouteCollection routes)
    {
        routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

        routes.MapRoute(
            name: "Default",
            url: "{controller}/{action}/{id}",
            defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
        );
    }
}
```

我们自定义一个路由：

```
public static void RegisterRoutes(RouteCollection routes)
{
    routes.IgnoreRoute("{resource}.axd/{*pathInfo}");

    routes.MapRoute(
        name: "Serial Number",
        url: "serial/{lettercase}",
        defaults: new { controller = "Home", action = "Serial", lettercase="upper" }
    );

    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Home", action = "Index", id = UrlParameter.Optional }
    );
}
```

它定义了一个路由，路由名称为"Serial Number", url以"serial"开头，含有一个lettercase参数，使用HomeController.Serial来处理，lettercase默认值为"upper".

现在在HomeController.cs中定义：

```
public ActionResult Serial(string lettercase)
{
    var serial = "ASP.NET mvc5";
    if (lettercase == "lower")
    {
        serial = serial.ToLower();
    }
    return Content(serial);
}
```

此时访问：http://localhost:17681/serial/ 或者 http://localhost:17681/serial/lower 都可以。

如果路由中没有包含{lettercase}，则可以使用querystring方式传递lettercase: http://localhost:17681/serial/?lettercase=lower:

```
routes.MapRoute(
    name: "Serial Number",
    url: "serial",
    defaults: new { controller = "Home", action = "Serial" }
);
```

vs快捷键：F5运行调试； ctrl+F5：运行但不调试，此时运行时可以修改代码；ctrl+shift+b：编译代码，可以在运行时重新加载而无需重启。

##3 返回类型

内建Action Result类型：

- ViewResult：渲染返回完整的网页
- PartialViewResult:渲染返回网页的一部分，用于Ajax比较多；
- ContentResult: 返回用户自定义的内容（text,xml)等；
- JsonResult: 返回Json类型
- RedirectToRouteResult:重定向

###3.1 PartialViewResult的例子

```
public ActionResult Index()
{
    return PartialView();
}
```

###3.2 JsonResult的例子

```
public ActionResult Serial(string lettercase)
{
    var serial = "ASP.NET mvc5";
    if (lettercase == "lower")
    {
        serial = serial.ToLower();
    }
    //return Content(serial);
    return Json(new {name = "serial", value = serial}, JsonRequestBehavior.AllowGet);
}
```

###3.3 RedirectToRouteResult的例子

```
public ActionResult Serial(string lettercase)
{
    var serial = "ASP.NET mvc5";
    if (lettercase == "lower")
    {
        serial = serial.ToLower();
    }
    return RedirectToAction("Index");
}
```

---
##4 Action Selector

###4.1 HttpPost

```
public ActionResult Contact()
{
    ViewBag.TheMessage = "有问题的话请留言哦~";

    return View();
}

[HttpPost]
public ActionResult Contact(string message)
{
    ViewBag.TheMessage = "感谢你的留言~";

    return View();
}
```

对应的视图

```
<form method="POST">
    <input type="text" name="message"/>
    <input type="submit"/>
</form>
```

####4.1.1 防止CSRF，使用`ValidateAntiForgeryToken`：
```
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult Create([Bind(Include = "ID,Title,ReleaseDate,Genre,Price")] Movie movie)
{
    if (ModelState.IsValid)
    {
        db.Movies.Add(movie);
        db.SaveChanges();
        return RedirectToAction("Index");
    }

    return View(movie);
}
```
对应的视图使用`@Html.AntiForgeryToken`：
```
@using (Html.BeginForm()) 
{
    @Html.AntiForgeryToken()
    
    <div class="form-horizontal">
        <h4>Movie</h4>
        <hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
        <div class="form-group">
            @Html.LabelFor(model => model.Title, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Title, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Title, "", new { @class = "text-danger" })
            </div>
        </div>
```

####4.1.2 验证Post请求 `ModelState.IsValid`
使用`ModelState.IsValid`来验证发送来的模型是否正常。

```
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult Edit([Bind(Include="ID,Title,ReleaseDate,Genre,Price")] Movie movie)
{
    if (ModelState.IsValid)
    {
        db.Entry(movie).State = EntityState.Modified;
        db.SaveChanges();
        return RedirectToAction("Index");
    }
    return View(movie);
}
```


---
###4.2 ActionName

```
[ActionName("about-the-site")]
public ActionResult About()
{
    ViewBag.Message = "Your application description page.";

    return View("About");
}
```

此时访问地址就是http://localhost:17681/Home/about-the-site

###4.3 Route

```
[Route("home/create")]
public ActionResult Create()
{
	
}
```

---

##5. 过滤器

常见的过滤器

###5.1 Authorize属性

```
[Authorize(Roles="administrator", Users="liulx")]
[HttpPost]
public ActionResult Create(Customer customer)
{
	db.Customers.Add(customer);
	db.SaveChanges();
	return RedirectToAction("Index");
}
```

`Authorize`可以不带参数，修饰class，如果class是`Authorize`修饰的，那么可以用[AllowAnonymous]修饰对应的方法允许匿名访问。

###5.2 Action filter

创建自定义的Action Filter：

- 继承`ActionFilterAttribute`类
- 重写`OnActionExecuting`方法，该方法在Action之前执行
- 重写`OnActionExecuted`方法，该方法在Action之后执行

```
public class MyLoggingFilterAttribute : ActionFilterAttribute
{
    public override void OnActionExecuted(ActionExecutedContext filterContext)
    {
        var request = filterContext.HttpContext.Request;
        // Logger.logRequest(request.UserHostAddress);
        base.OnActionExecuted(filterContext);
    }
}
```

调用

```
[MyLoggingFilter]
public ActionResult Index()
{
    // throw new StackOverflowException();
    return View();
}
```

要想在全局应用自定义的Filter，可以这样：

```
public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
    	//添加自定义Filter
        filters.Add(new MyLoggingFilterAttribute());
        filters.Add(new HandleErrorAttribute());
    }
}
```

###5.3 Result Filter

```
[OutputCache(Duration=1800)]
public ActionResult Index()
{
    // throw new StackOverflowException();
    return View();
}

[OutputCache(Duration=1800, VaryByParam="id")]
public ActionResult Details(int id)
{
    Product p = db.Products.Find(id);
    return View(p);
}
```

###5.4 Exception Filter

```
[HandleError(View="MyError")]
public ActionResult Index()
{
    // throw new StackOverflowException();
    return View();
}

[HandleError(View="MyError2", ExceptionType=typeof(DivideByZeroException))]
public ActionResult Details(int id)
{
    Product p = db.Products.Find(id);
    return View(p);
}
```

