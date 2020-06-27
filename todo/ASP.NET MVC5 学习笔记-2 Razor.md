Title: ASP.NET MVC5 学习笔记-2 Razor
Date: 2014-10-31 9:28
Category: DotNet
Tags: DotNet, Web, ASP.NET MVC
Author: 刘理想

[TOC]

# ASP.NET MVC5 学习笔记-2 Razor

##1. Razor

```
@*注释*@

你在用 @Request.Browser.Browser, 发送邮件给support@qq.com, 转义@@qq

@{
    var amounts = new List<Decimal> {100, 25.00m, 30.0m, -40}; 
}

<ul>
    @foreach (decimal amount in amounts)
    {
        <li>@formatAmount(amount)</li>
        <text>普通文本</text>
        @:普通文本
    }
</ul>

@helper formatAmount(decimal amount)
{
    var color = "green";
    if (amount < 0)
    {
        color = "red";
    }
    <span style="color:@color">@String.Format("{0:c}", amount)</span>
}
```

##2. Layout

###2.1 模板继承

`Views`文件夹下`_ViewStart.cshtml`定义了全局的Layout：

```
@{
    Layout = "~/Views/Shared/_Layout.cshtml";
}
```

如果给某个控制器指定特定的layout，就是在这个控制器对应的文件夹下再建一个`_ViewStart.cshmtl`，里面内容与上面相同。

如果想给某个特定Action指定layout，可以这样：

```
@{
    ViewBag.Title = "Home Page";
    Layout = "MyLayout.cshtml";
}
```

###2.2 layout定义

```
<div class="container body-content">
    @RenderBody()
    <hr />
    <footer>
        <p>&copy; @DateTime.Now.Year - 我的 ASP.NET 应用程序</p>
    </footer>
</div>

@Scripts.Render("~/bundles/jquery")
@Scripts.Render("~/bundles/bootstrap")
@RenderSection("scripts", required: false)
```

RenderBody()就是默认全部。RenderSection()在子模板中可以省略，但定义如下：

```@section scripts
{
    <script> var testing = true;</script>
}
```

##3. Html Helpers

###3.1 Html.ActionLink

```
@Html.ActionLink("连接名", "Action", "Controller", new {paramter=""}, new {@class="css类，其他类不用加@"})
@Html.ActionLink("连接名", "Action", "Controller")
```

###3.2 Html.Partial

包含部分模板

```
@Html.Partial("_LoginPartial")
```

###3.3 Html.BeginForm

`Html.BeginForm("action", "form", Method, css)`

```
using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm", @class = "navbar-right" }))
{
//注意着一行，防止CSRF
@Html.AntiForgeryToken()

<ul class="nav navbar-nav navbar-right">
    <li>
        @Html.ActionLink("你好 " + User.Identity.GetUserName() + "!", "Manage", "Account", routeValues: null, htmlAttributes: new { title = "管理" })
    </li>
    <li><a href="javascript:document.getElementById('logoutForm').submit()">注销</a></li>
</ul>
}
```

如果使用了```@Html.AntiForgeryToken()```，则对应的Action应该添加`ValidateAntiForgeryToken`

```
[HttpPost]
[ValidateAntiForgeryToken]
public ActionResult LogOff()
{
    AuthenticationManager.SignOut();
    return RedirectToAction("Index", "Home");
}
```

###3.4 Html.Action

输出Action的结果作为页面的一部分

```
<footer>
    <p>&copy; @DateTime.Now.Year - 我的 ASP.NET 应用程序 @Html.Action("Serial", "Home", new{lettercase="lower"})</p>
</footer>
```

###3.5 下拉框
代码
```
public ActionResult Index(string movieGenre, string searchString)
{
    var GenreLst = new List<string>();

    var GenreQry = from d in db.Movies
                   orderby d.Genre
                   select d.Genre;

    GenreLst.AddRange(GenreQry.Distinct());
    ViewBag.movieGenre = new SelectList(GenreLst);
    ...
}
```
对应的视图
```
@using (Html.BeginForm("Index", "Movies", FormMethod.Get))
{
<p>
    Genre: @Html.DropDownList("movieGenre", "All")
    Title: @Html.TextBox("SearchString")
    <input type="submit" value="Filter" />
</p>
}
```
使用`Html.DropDownList`和`Html.TextBox`等中绑定的名称和ViewBag中的值相同时可以自动绑定。


##4. Bundling和Minification

```
@Styles.Render("~/Content/css")
@Scripts.Render("~/bundles/modernizr")
@Scripts.Render("~/bundles/jquery")
@Scripts.Render("~/bundles/bootstrap")
```

```
public class BundleConfig
{
    // 有关绑定的详细信息，请访问 http://go.microsoft.com/fwlink/?LinkId=301862
    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));

        bundles.Add(new ScriptBundle("~/bundles/jqueryval").Include(
                    "~/Scripts/jquery.validate*"));

        // 使用要用于开发和学习的 Modernizr 的开发版本。然后，当你做好
        // 生产准备时，请使用 http://modernizr.com 上的生成工具来仅选择所需的测试。
        bundles.Add(new ScriptBundle("~/bundles/modernizr").Include(
                    "~/Scripts/modernizr-*"));

        bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
                  "~/Scripts/bootstrap.js",
                  "~/Scripts/respond.js"));

        bundles.Add(new StyleBundle("~/Content/css").Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
        //压缩
        BundleTable.EnableOptimizations = true;
    }
}
```

##5. Bootstrap

ASP.NET MVC 5默认支持BootStrap