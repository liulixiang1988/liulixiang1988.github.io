Title: ASP.NET MVC5 学习笔记-3 Model
Date: 2014-10-31 20:48
Category: DotNet
Tags: DotNet, Web, ASP.NET MVC
Author: 刘理想

[TOC]

#ASP.NET MVC5 学习笔记-3 Model

##1. Model

###1.1 添加一个模型

**注意**，添加属性时可以输入"prop"，会自动输入代码段。

```
public class CheckoutAccount
{
    public int Id { get; set; }

    public string AccountNumber { get; set; }

    public string FirstName { get; set; }

    public string LastName { get; set; }

    public string Balance { get; set; }
}
```

###1.2 添加一个“包含读写操作的MVC 5控制器”CheckingAccountController

去掉Details的参数Id，因为现在我没还没有数据库。

```
public ActionResult Details()
{
    return View();
}
```

###1.3 在Details上添加视图

在Details动作上右键，添加视图：

- 视图名称`Details`
- 模板`Details`
- 模型`CheckoutAccount`
- 数据上下文类：空
- 创建为分布视图：不勾选
- 引用脚本库：不勾选
- 使用布局页：默认布局

```
@model AspNetMVCEssential.Models.CheckoutAccount

@{
    ViewBag.Title = "Details";
}

<h2>Details</h2>

<div>
    <h4>CheckoutAccount</h4>
    <hr />
    <dl class="dl-horizontal">
        <dt>
            @Html.DisplayNameFor(model => model.AccountNumber)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.AccountNumber)
        </dd>

        <dt>
            @Html.DisplayNameFor(model => model.FirstName)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.FirstName)
        </dd>

        <dt>
            @Html.DisplayNameFor(model => model.LastName)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.LastName)
        </dd>

        <dt>
            @Html.DisplayNameFor(model => model.Balance)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.Balance)
        </dd>

    </dl>
</div>
<p>
    @Html.ActionLink("Edit", "Edit", new { id = Model.Id }) |
    @Html.ActionLink("Back to List", "Index")
</p>
```

说明：

- 第一行代码指明模板Model类型，任何@Model都是`CheckoutAccount`类型。
- `Html.DisplayNameFor(model => model.AccountNumber)`模型属性的名称
- `Html.DisplayFor(model => model.AccountNumber)`模型属性的值

###1.4 添加首页到Details页面的链接

```
<div class="col-md-6 margin-top-20">
    <a href="@Url.Action("Details", "CheckingAccount")" class="btn btn-primary btn-lg btn-block"><span class="glyphicon glyphicon-question-sign"></span>余额查询</a>
</div>
```

`@Url.Action("action", "controller")`返回URL，而不是`<a>`

---

##2. 显示和验证模型属性

###2.1 清理Details视图

```
@model AspNetMVCEssential.Models.CheckoutAccount

@{
    ViewBag.Title = "Details";
}
<div class="row">
    <div class="col-md-6 col-md-offset-3">
        <h2>账户查询</h2>
        <dl class="dl-horizontal">
            <dt>
                @Html.DisplayNameFor(model => model.AccountNumber)
            </dt>

            <dd>
                @Html.DisplayFor(model => model.AccountNumber)
            </dd>

            <dt>
                @Html.DisplayNameFor(model => model.FirstName)
            </dt>

            <dd>
                @Html.DisplayFor(model => model.FirstName)
            </dd>

            <dt>
                @Html.DisplayNameFor(model => model.LastName)
            </dt>

            <dd>
                @Html.DisplayFor(model => model.LastName)
            </dd>

            <dt>
                @Html.DisplayNameFor(model => model.Balance)
            </dt>

            <dd>
                @Html.DisplayFor(model => model.Balance)
            </dd>
        </dl>
    </div>
</div>
```

###2.2 修改Model

`[Display(Name="Account")]`

```
public class CheckoutAccount
{
    public int Id { get; set; }

    [Display(Name = "账户")]
    public string AccountNumber { get; set; }

    public string FirstName { get; set; }

    public string LastName { get; set; }
    [Display(Name = "姓名")]
    public string Name { get { return FirstName + " " + LastName; } }
    [DataType(DataType.Currency)]
    public int Balance { get; set; }
}
```

###2.3 创建Create对应的视图

此时要选择引用脚本库。

```
@using (Html.BeginForm()) 
{
    @Html.AntiForgeryToken()
    
    <div class="form-horizontal">
        <h4>CheckoutAccount</h4>
        <hr />
        @Html.ValidationSummary(true)

        <div class="form-group">
            @Html.LabelFor(model => model.AccountNumber, new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.AccountNumber)
                @Html.ValidationMessageFor(model => model.AccountNumber)
            </div>
        </div>
     ...
 }
 ```

- `@Html.LabelFor(model=>model.Account)`产生Label
- `@Html.EditorFor(model=>model.Account)`产生input
- `@Html.ValidationMessageFor(model=>model.AccountNumber)`验证信息
- `@Html.ValidationSummary(true)`验证汇总

###2.4 修改CheckAccount

```
[Required]
//[StringLength(10, MinimumLength = 6)]
[RegularExpression(@"\d{6,10}", ErrorMessage = "账户必须是6-10个字符")]
[Display(Name = "账户")]
public string AccountNumber { get; set; }

[Required]
public string FirstName { get; set; }

[Required]
public string LastName { get; set; }
[Display(Name = "姓名")]
public string Name { get { return FirstName + " " + LastName; } }

[Required]
[DataType(DataType.Currency)]
public int Balance { get; set; }
```

都是一些验证字段，注意每个都可以有`ErrorMessage`参数。

--

##3. ViewModel

ViewModel不在数据库中存储，专门用于处理Form，类似Django中的Form类。

###3.1 ViewModel定义

打开Models文件夹下的AccountViewModel，我们看到ViewModel定义与普通Model并没有明显区别，只是命名约定都是以"ViewModel"结尾。

```
public class LoginViewModel
{
    [Required]
    [Display(Name = "用户名")]
    public string UserName { get; set; }

    [Required]
    [DataType(DataType.Password)]
    [Display(Name = "密码")]
    public string Password { get; set; }

    [Display(Name = "记住我?")]
    public bool RememberMe { get; set; }
}
```

###3.2 ViewModel在Action中的应用

```
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Login(LoginViewModel model, string returnUrl)
{
    if (ModelState.IsValid)
    {
        var user = await UserManager.FindAsync(model.UserName, model.Password);
        if (user != null)
        {
            await SignInAsync(user, model.RememberMe);
            return RedirectToLocal(returnUrl);
        }
        else
        {
            ModelState.AddModelError("", "Invalid username or password.");
        }
    }

    // 如果我们进行到这一步时某个地方出错，则重新显示表单
    return View(model);
}
```

可以看到ViewModel只是接收用户的输入并封装，然后再使用封装的字段查找数据库中对应的Model。

###3.3 ViewModel在视图中的应用

```
@model AspNetMVCEssential.Models.LoginViewModel

@{
    ViewBag.Title = "登录";
}

<h2>@ViewBag.Title。</h2>
<div class="row">
    <div class="col-md-8">
        <section id="loginForm">
            @using (Html.BeginForm("Login", "Account", new { ReturnUrl = ViewBag.ReturnUrl }, FormMethod.Post, new { @class = "form-horizontal", role = "form" }))
            {
                @Html.AntiForgeryToken()
                <h4>使用本地帐户登录。</h4>
                <hr />
                @Html.ValidationSummary(true)
                <div class="form-group">
                    @Html.LabelFor(m => m.UserName, new { @class = "col-md-2 control-label" })
                    <div class="col-md-10">
                        @Html.TextBoxFor(m => m.UserName, new { @class = "form-control" })
                        @Html.ValidationMessageFor(m => m.UserName)
                    </div>
                </div>
                ...
            }
             ...
```

ViewModel在视图中使用与普通Model并没有区别。

---

##4. 数据库对象和Entity Framework

###4.1 打开IdentityModels.cs文件

打开文件后，我们看到两个类：

- `ApplicationUser`继承自`IdentityUser`,后者是用户类
- `ApplicationDbContext`继承自`IdentityDbContext<ApplicationUser>`，后者又继承自`DbContext`，数据库上下文类，直接与数据库进行交互。

###4.2 添加`CheckoutAccount`到数据库上下文

```
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext()
        : base("DefaultConnection")
    {
    }

    public DbSet<CheckoutAccount> CheckoutAccounts { get; set; }
}
```

这样就可以操作数据库了。

###4.3 添加Model外键

我们对`CheckoutAccount`添加如下两个属性：

```
public virtual ApplicationUser User { get; set; }
public string ApplicationUserId { get; set; }
```

其中的User使用`virtual`关键字是用来延迟加载的， 第二个属性用来指明外键，Entity Framework很聪明，能理解你的意思是指定它为外键。

###4.4 在Action中使用

我们让用户在注册时系统自动生成一个CheckoutAccount记录：

```
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Register(RegisterViewModel model)
{
    if (ModelState.IsValid)
    {
        var user = new ApplicationUser() { UserName = model.Email, Email = model.Email};
        var result = await UserManager.CreateAsync(user, model.Password);
        if (result.Succeeded)
        {
            //创建CheckoutAcount
            var db = new ApplicationDbContext();
            var checkoutAccount = new CheckoutAccount
            {
                AccountNumber = "00000123",
                FirstName = model.FirstName,
                LastName = model.LastName,
                Balance = 0,
                ApplicationUserId = user.Id
            };
            db.CheckoutAccounts.Add(checkoutAccount);
            db.SaveChanges();

            await SignInAsync(user, isPersistent: false);
            return RedirectToAction("Index", "Home");
        }
        else
        {
            AddErrors(result);
        }
    }

    // 如果我们进行到这一步时某个地方出错，则重新显示表单
    return View(model);
}
```

运行，注册账户，我们会发现自动注册了`CheckoutAccount`对象，并且添加了外键。

###4.5 Model在视图中的使用以及`@Model`关键字

之前使用`ViewBag`来向模板传递对象，`ViewBag`是动态类型的。ASP.NET MVC还提供传递强类型数据到模板的方法。强类型的好处是VS提供了智能提示。
比如之前我们使用了
```
return View(model);
```

检查对应的视图:
```
@model MvcMovie.Models.Movie

@{
    ViewBag.Title = "Details";
}

<h2>Details</h2>

<div>
    <h4>Movie</h4>
	<hr />
    <dl class="dl-horizontal">
        <dt>
            @Html.DisplayNameFor(model => model.Title)
        </dt>
         @*Markup omitted for clarity.*@        
    </dl>
</div>
<p>
    @Html.ActionLink("Edit", "Edit", new { id = Model.ID }) |
    @Html.ActionLink("Back to List", "Index")
</p>
```
我们看到在模板头部我们添加了`@model`语句，然后我们就可以在视图中使用`Model`对象来访问对应的对象及其属性。

对于列表型的数据，我们可以使用如下方法:
```
@model IEnumerable<MvcMovie.Models.Movie> 
@foreach (var item in Model) {
    <tr>
        <td>
            @Html.DisplayFor(modelItem => item.Title)
        </td>
        <td>
            @Html.ActionLink("Edit", "Edit", new { id=item.ID }) 
        </td>
    </tr>
}
```

---

##5. Entity Framework Code First迁移

现在我们想修改数据库，当然可以直接使用SQL Server修改，但也可以通过Code First迁移进行修改。

###5.1 修改CheckoutAccount

```
[Required]
[StringLength(10)]
[Column(TypeName = "varchar")]
[RegularExpression(@"\d{6,10}", ErrorMessage = "账户必须是6-10个字符")]
[Display(Name = "账户")]
public string AccountNumber { get; set; }
```

###5.2 打开迁移

打开NuPackage程序包管理器控制台。

```
PM> Enable-Migrations -ContextType ApplicationDbContext
正在检查上下文的目标是否为现有数据库...
检测到使用数据库初始值设定项创建的数据库。已搭建与现有数据库对应的迁移“201410311619291_InitialCreate”的基架。若要改用自动迁移，请删除 Migrations 文件夹并重新运行指定了 -EnableAutomaticMigrations 参数的 Enable-Migrations。
已为项目 AspNetMVCEssential 启用 Code First 迁移。
```

###5.3 添加迁移

```
PM> Add-Migration AccountNumberChanges
正在为迁移“AccountNumberChanges”搭建基架。
此迁移文件的设计器代码包含当前 Code First 模型的快照。在下一次搭建迁移基架时，将使用此快照计算对模型的更改。如果对要包含在此迁移中的模型进行其他更改，则您可通过再次运行“Add-Migration AccountNumberChanges”重新搭建基架。
```

也可以生成迁移脚本：

```
PM> Update-Database -Script
正在应用显式迁移: [201410311643166_AccountNumberChanges]。
正在应用显式迁移: 201410311643166_AccountNumberChanges。
```

此时系统会自动弹出一个包含Script的迁移。

###5.4 应用迁移

```
PM> Update-Database -Verbose
Using StartUp project 'AspNetMVCEssential'.
Using NuGet project 'AspNetMVCEssential'.
指定“-Verbose”标志以查看应用于目标数据库的 SQL 语句。
目标数据库为: “aspnet-AspNetMVCEssential-20141031091551”(DataSource: .，提供程序: System.Data.SqlClient，来源: Configuration)。
正在应用显式迁移: [201410311643166_AccountNumberChanges]。
正在应用显式迁移: 201410311643166_AccountNumberChanges。
...
正在运行 Seed 方法。
```

###5.5 回滚迁移

```
PM> Update-Databse -TargetMigration IntialCreate
```

###5.6 自动迁移

打开`Migrations`文件夹下的`Configuration`

```
internal sealed class Configuration : DbMigrationsConfiguration<AspNetMVCEssential.Models.ApplicationDbContext>
{
    public Configuration()
    {
        //设置自动迁移
        AutomaticMigrationsEnabled = true;

        ContextKey = "AspNetMVCEssential.Models.ApplicationDbContext";
    }
    //...
}
```

此后，发生变更时，直接使用:

```
PM> Update-Database -Verbose
Using StartUp project 'AspNetMVCEssential'.
Using NuGet project 'AspNetMVCEssential'.
指定“-Verbose”标志以查看应用于目标数据库的 SQL 语句。
目标数据库为: “aspnet-AspNetMVCEssential-20141031091551”(DataSource: .，提供程序: System.Data.SqlClient，来源: Configuration)。
没有挂起的显式迁移。
正在应用自动迁移: 201410311656382_AutomaticMigration。
System.Data.Entity.Migrations.Infrastructure.AutomaticDataLossException: 未应用自动迁移，因为自动迁移会导致数据丢失。如果要在可能导致数据丢失的情况下允许应用自动迁移，请在 DbMigrationsConfiguration 上将 AutomaticMigrationDataLossAllowed 设置为 "true"。也可以将 Update-Database 与 "-Force" 选项一起使用，或者构建基架执行显式迁移。
   在 System.Data.Entity.Migrations.DbMigrator.AutoMigrate(String migrationId, VersionedModel sourceModel, VersionedModel targetModel, Boolean downgrading)
。。。
   在 System.Data.Entity.Migrations.MigrationsDomainCommand.Execute(Action command)
未应用自动迁移，因为自动迁移会导致数据丢失。如果要在可能导致数据丢失的情况下允许应用自动迁移，请在 DbMigrationsConfiguration 上将 AutomaticMigrationDataLossAllowed 设置为 "true"。也可以将 Update-Database 与 "-Force" 选项一起使用，或者构建基架执行显式迁移。
```

我们看到上面报了一些错误，这是因为可能会造成丢失，这就让我们可以强制更新：

```
PM> Update-Database -Verbose -Force
```