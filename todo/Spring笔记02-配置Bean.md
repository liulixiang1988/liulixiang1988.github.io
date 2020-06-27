Title: Spring笔记02-配置Bean
Date: 2016-08-11 10:22
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 2.1 构造函数和Setter注入

之前我们基于注解的配置是这样的：

```
@Configuration
@ComponentScan(basePackages = "lx.spring.core")
public class AppConfig {

    @Autowired
    private DataSource dataSource;

    @Autowired @Qualifier("redSox")
    private Team home;

    @Autowired @Qualifier("cubs")
    private Team away;

    @Bean
    public Game game() {
        BaseballGame game = new BaseballGame(home, away);
        game.setDataSource(dataSource);
        return game;
    }
}
```

我们知道如果没有`@Qualifier`注解，就会报错。但是如果我们把`home`和`away`去掉，然后添加一个集合，像下面这样：

```
...
public class AppConfig {
	...
    @Autowired
    private List<Team> teams;

    @Bean
    public Game game() {
        BaseballGame game = new BaseballGame(teams.get(0), teams.get(1));
        ...
    }
}
```

如果我们有多个相同类型的Bean，但是是以单个变量声明的，如

```
@Autowired @Qualifier("redSox")
private Team home;
```

如果没有`@Qualifier`注解就会报错，但是想前面例子是用集合的形式：

```
@Autowired
private List<Team> teams;
```

就不会报错，他会扫描所有这个类型的Bean，然后放到集合中。

有的时候，我们需要确定是使用构造器注入还是setter方法注入，这里有一个指导原则：

如果这个依赖是必须的，比如数据库驱动等，那就使用构造器注入，这样初始化的时候就必须提供参数；如果依赖不是必须的，则使用setter注入。

在Bean构造好后，我们也可以修改，比如，我们在`RunDemo.java`中添加修改：

```
public class RunDemo {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        Game game = context.getBean("game", Game.class);
        System.out.println(game.playGame());

        Team royals = context.getBean("royals", Team.class);
        game.setAwayTeam(royals);
        System.out.println(game.playGame());
        ...
    }
}
```

## 2.2 Bean Scopes

其实之前对Bean的用法都不正确，但这是最简单的用法。

我们队`BaseballGame`添加`toString()`方法：

```
@Override
public String toString() {
    return String.format("Game between %s at %s", awayTeam.getName(), homeTeam.getName());
}
```

然后修改`RunDemo`中的调用：

```
public class RunDemo {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        Team royals = context.getBean("royals", Team.class);

        Game game1 = context.getBean("game", Game.class);
        System.out.println(game1);

        Game game2 = context.getBean("game", Game.class);
        game2.setAwayTeam(royals);
        System.out.println(game2);

        System.out.println(game1);
    }
}
//输出：
//Game between Bostom Red Sox at Chicago Cubs
//Game between Kansas City Royals at Chicago Cubs
//Game between Kansas City Royals at Chicago Cubs
```

我们看一下输出，`game1`和`game2`输出是一样的，为什么？

**因为Spring Bean是单例模式**,但是这也不难改变。如果我们希望哪个Bean不是单例的，只要添加注解`@Scope("prototype")`就可以了。比如：

```
@Configuration
@ComponentScan(basePackages = "lx.spring.core")
public class AppConfig {
	...
    @Bean @Scope("prototype") //注意这里给Bean添加了@Scope
    public Game game() {
        BaseballGame game = new BaseballGame(teams.get(0), teams.get(1));
        game.setDataSource(dataSource);
        return game;
    }
}
```

`@Scope("prototype")`表示每次调用时都返回一个新的Bean。

这里体现了Spring的一个哲学，它意味着Spring管理的主要是可以看做是单例的，比如Service， Repository， 或者数据库连接池。Spring致力于提供基础设施。

在[上一节](http://liulixiang1988.github.io/springbi-ji-01-gou-jian-ying-yong.html)我们用Java进行配置：

```
@Configuration
public class AppConfig {
    @Bean
    public Game game() {
        return new BaseballGame(redSox(), cubs());
    }

    @Bean
    public Team redSox() {
        return new RedSox();
    }
    ...
}
```

注意在`game()`里，我们又调用了`redSox()`，这是一个函数调用。那就有同学会问了，这难道不会产生新的Bean吗？不会的。这是为什么呢？因为Spring会重新继承配置，这里是`AppConfig`，然后重写这里面的方法。在**子类**中优点类似这样：

```
public Team redSox() {
	if (redSox 已经在 appContext 中) {
		return redSox;
	} else {
		//调用super.redSox()
		//添加到appContext中
		//return redSox
	}
}
```

现在，我们再来看`@Scope`注解，默认值是`@Scope("singleton")`，还有`@Scope("prototype")`，如果在Spring MVC中，则还有：

- `request`
- `session`

当然你也可以定义自己的`Scope`，但我们还是应该记住：**默认情况下Spring的Bean是单例的**。

## 2.3 工厂方法和工厂Bean

工厂方法和工厂Bean一般主要用于XML配置Bean时。

### 2.3.1 工厂方法

我们来看一看后面如何在XML中调用工厂方法。


`java.text.NumberFormat`是一个抽象类，所以也就没法直接生成对象，但是它有一些静态工厂方法可以用于生成对象。我们在XML中添加一个它的`Bean`就需要像这样了：

```xml
<bean id="nf" class="java.text.NumberFormat" factory-method="getCurrencyInstance" />
```

注意这里的**factory-method**，此时，我们可以再应用中这样调用：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
NumberFormat nf = context.getBean(NumberFormat.class);
double amount = 12345678.9012345;
System.out.println(nf.format(amount));
//输出：
//￥12,345,678.90
```

从这个我们可以看出，Spring需要知道怎么去创建实例（构造方法还是工厂方法）才能给我们创建Bean。

### 2.3.2 工厂Bean

有时候，我们需要用别的类中的工厂方法来创建类，这个时候我们就需要使用工厂Bean。

这里我们举一个例子：

```xml
<bean id="factory" class="javax.xml.parsers.DocumentBuilderFactory" factory-method="newInstance" />
<bean id="documentBuilder" class="javax.xml.parsers.DocumentBuilder" factory-bean="factory" factory-method="newDocumentBuilder"/>
```

我们可以看到`documentBuilder`是通过`factory`这个Bean的`newDocumentBuilder`方法中调用的。

当然，如果我们用的是Java配置，直接这么写就可以了：

```
@Bean
public NumberFormat nf() {
	return NumberFormat.getCurrencyInstance();
}
```

## 2.4 初始化和销毁

有时候，我们希望在Bean创建后和销毁前做一些动作。

比如，对于我们之前定义的`BaseballGame`，我们添加两个方法`startGame`和`endGame`：

```
public class BaseballGame implements Game {
	...    
    public void startGame() {
        System.out.println("Start game!");
    }
    
    public void endGame() {
        System.out.println("End game!");
    }
    ...
}
```

### 2.4.1 initMethod和destroyMethod属性

下面是重点，我们看看如何在创建后和销毁前调用`startGame`和`endGame`，再次返回到`AppConfig`中：

```
@Bean(initMethod = "startGame", destroyMethod = "endGame")
@Scope("prototype")
public Game game() {
    BaseballGame game = new BaseballGame(teams.get(0), teams.get(1));
    game.setDataSource(dataSource);
    return game;
}
```

**注意，我们不能给intMethod和destroyMethod传递参数，因此必须要求对应的方法是无餐的。**

这里，我们用`@Bean`的`initMethod`和`destroyMethod`来指定对应的方法，接着我们再次执行下面的代码：

```
Game game1 = context.getBean("game", Game.class);
System.out.println(game1);
//输出
//Start game!
//Game between Bostom Red Sox at Chicago Cubs
```

但是我们执行时发现并没有输出销毁指定的方法，这是为什么？因为JVM可能这会并没有执行垃圾回收，所以对象也没有销毁。

### 2.4.2 @PostConstruct 和 @PreDestroy

除了使用`@Bean(initMethod, destroyMethod)`， Java本身也提供了一些注解让我们来做初始化后和销毁前的一些工作，他们就是`@PostConstruct`和`@PreDestroy`，我们直接把它们应用到对应的方法上即可。

```
@PostConstruct
public void startGame() {
    System.out.println("Start game!");
}

@PreDestroy
public void endGame() {
    System.out.println("End game!");
}
```

但是这个方法我们必须有Bean对应的源码才行。

### 2.4.3 AnnotationConfigApplicationContext.close方法

我们之前看到输出时并没有输出对应的销毁前方法，当然我们也有方法让Bean销毁，那就是用`AnnotationConfigApplicationContext.close()`方法，注意这个方法会自动调用，但是对于`Scope("prototype")`的无效，必须是单例的才行：

```
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
Game game1 = context.getBean("game", Game.class);
System.out.println(game1);
context.close();
```

