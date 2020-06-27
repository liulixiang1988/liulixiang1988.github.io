Title: ASP.NET MVC5 学习笔记-5 测试
Date: 2014-12-04 22:15
Category: DotNet
Tags: DotNet, Web, ASP.NET MVC
Author: 刘理想

#ASP.NET MVC5 学习笔记-5 测试

##1. 测试步骤

- 准备
- 执行
- 检查

##2. 创建单元测试

**注意：单元测试不要包含数据库操作，包含数据库操作的一般成为集成测试。**

###2.1 编写测试代码

```
namespace AspNetMVCEssential.Tests.Controllers
{
    [TestClass]
    public class HomeControllerTest
    {
        [TestMethod]
        public void Index()
        {
            // Arrange
            HomeController controller = new HomeController();

            // Act
            ViewResult result = controller.Index() as ViewResult;

            // Assert
            Assert.IsNotNull(result);
        }

        [TestMethod]
        public void About()
        {
            // Arrange
            HomeController controller = new HomeController();

            // Act
            ViewResult result = controller.About() as ViewResult;

            // Assert
            Assert.AreEqual("Your application description page.", result.ViewBag.Message);
        }

        [TestMethod]
        public void Contact()
        {
            // Arrange
            HomeController controller = new HomeController();

            // Act
            ViewResult result = controller.Contact() as ViewResult;

            // Assert
            Assert.IsNotNull(result);
        }
    }
}
```

###2.2 运行测试

选定菜单“测试”->“运行”->运行所有测试或者未成功的测试。
可以在“测试”->“窗口”->“测试资源管理器”中对测试进行管理。

---

##3 Mock测试

Mock测试就是在测试过程中，对于某些不容易构造或者不容易获取的对象，用一个虚拟的对象来创建以便测试的测试方法。

我们要对`TransactionController.cs`进行测试，直接操作数据库不行，怎么办呢？我们可以使用虚拟的接口来进行测试。这需要我们更改一些代码.

###3.1 首先，我们更改`TransactionController.cs`

更改db为`IApplicationDbContext`类型，并且添加构造方法：

```
private IApplicationDbContext db;

//实际使用
public TransactionController()
{
    db = new ApplicationDbContext();
}

//生成使用
public TransactionController(IApplicationDbContext dbContext)
{
    db = dbContext;
}
```

###3.2 创建IApplicationDbContext对象

```
public interface IApplication
{
    IDbSet<CheckingAccount> CheckingAccounts{get;set;}
    IDbSet<Transaction> Transactions{get; set;}
    
    public int SaveChanges();
}
```

让实际的ApplicationDbContext实现IApplicationDbContext。

另外创建一个假的FakeApplicationDbContext实现IApplicationDbContext
```
public class FakeApplicationDbContext:IApplicationDbContext
{
    IDbSet<CheckingAccount> CheckingAccounts{get;set;}
    IDbSet<Transaction> Transactions{get; set;}
    
    public int SaveChanges(){return 0}
}
```

再创建一个FakeDbSet
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace EntityExtensions {
    public class FakeDbSet<T> : System.Data.Entity.IDbSet<T> where T : class {
        private readonly List<T> list = new List<T>();

        public FakeDbSet() {
            list = new List<T>();
        }

        public FakeDbSet(IEnumerable<T> contents) {
            this.list = contents.ToList();
        }

        #region IDbSet<T> Members

        public T Add(T entity) {
            this.list.Add(entity);
            return entity;
        }

        public T Attach(T entity) {
            this.list.Add(entity);
            return entity;
        }

        public TDerivedEntity Create<TDerivedEntity>() where TDerivedEntity : class, T {
            throw new NotImplementedException();
        }

        public T Create() {
            throw new NotImplementedException();
        }

        public T Find(params object[] keyValues) {
            throw new NotImplementedException();
        }

        public System.Collections.ObjectModel.ObservableCollection<T> Local {
            get {
                throw new NotImplementedException();
            }
        }

        public T Remove(T entity) {
            this.list.Remove(entity);
            return entity;
        }

        #endregion

        #region IEnumerable<T> Members

        public IEnumerator<T> GetEnumerator() {
            return this.list.GetEnumerator();
        }

        #endregion

        #region IEnumerable Members

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator() {
            return this.list.GetEnumerator();
        }

        #endregion

        #region IQueryable Members

        public Type ElementType {
            get { return this.list.AsQueryable().ElementType; }
        }

        public System.Linq.Expressions.Expression Expression {
            get { return this.list.AsQueryable().Expression; }
        }

        public IQueryProvider Provider {
            get { return this.list.AsQueryable().Provider; }
        }

        #endregion
    }
}
```

另外一种方法是使用
```
Install-Package FakeDbSet
```

##4 TDD

###4.1 TDD基本流程

创建失败的测试
用最简单的方法实现
优化

###4.2 创建测试

注意，测试应该也引用EF。

