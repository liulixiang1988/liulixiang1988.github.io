Title: Spring笔记01-构建应用
Date: 2016-08-10 17:29
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 1.1 一个Spring的简单Java应用


### 1.1.1 创建应用
这里，我们用Gradle创建Spring的应用。

```
group 'lx.spring.core'
version '1.0-SNAPSHOT'

apply plugin: 'java'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
    compile group: 'org.springframework', name: 'spring-context', version: '4.3.2.RELEASE'
    testCompile group: 'org.springframework', name: 'spring-test', version: '4.3.2.RELEASE'
    testCompile group: 'junit', name: 'junit', version: '4.11'
}
```

### 1.1.2 定义entity

spring希望我们尽可能的使用接口，因此，我们声明，`Team`, `Game`接口，然后创建对应的实现：

```
public interface Team {
    String getName();
}

public interface Game {
    void setHomeTeam(Team team);
    Team getHomeTeam();
    void setAwayTeam(Team team);
    Team getAwayTeam();
    String playGame();
}
```

对应的实现类：

```
public class Cubs implements Team {
    @Override
    public String getName() {
        return "Chicago Cubs";
    }
}

public class RedSox implements Team {
    @Override
    public String getName() {
        return "Bostom Red Sox";
    }
}

public class Royals implements Team {
    @Override
    public String getName() {
        return "Kansas City Royals";
    }
}

public class BaseballGame implements Game {
    private Team homeTeam;
    private Team awayTeam;

    public BaseballGame() {
    }

    public BaseballGame(Team homeTeam, Team awayTeam) {
        this.homeTeam = homeTeam;
        this.awayTeam = awayTeam;
    }

    @Override
    public Team getHomeTeam() {
        return homeTeam;
    }

    @Override
    public void setHomeTeam(Team homeTeam) {
        this.homeTeam = homeTeam;
    }

    @Override
    public Team getAwayTeam() {
        return awayTeam;
    }

    @Override
    public void setAwayTeam(Team awayTeam) {
        this.awayTeam = awayTeam;
    }

    @Override
    public String playGame() {
        return Math.random() < 0.5 ? getHomeTeam().getName() :
                getAwayTeam().getName();
    }
}
```

### 1.1.3 使用Java注解来进行调用

为了让我们的应用能够使用定义的entity，我们需要做配置，配置的方法多种多样，我们这里使用Java对象进行配置，创建一个配置类`AppConfig`:

```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

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

    @Bean
    public Team cubs() {
        return new Cubs();
    }
}
```

注意这里的`game()`方法中调用了`redSox()`和`cubs()`方法，后面我们会进行讲解。

### 1.1.4 运行应用

现在有了bean和对应的config，我们可以对他们进行调用了。

```
import lx.spring.core.entities.Game;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class RunDemo {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        Game game = context.getBean("game", Game.class);
        System.out.println(game.playGame());
    }
}
```

由此可见Spring隐藏了细节，让代码变得简单。

## 1.2 基于Java的配置

基于Java的配置是目前Spring应用的推荐做法。之前有做法是基于XML进行配置的。

为什么现在推荐用java进行配置呢？

因为这样做能让配置集中，有时候一个java文件就能包含所有配置了。其次，能让IDE进行错误提示。最后，可以利用Java语言的强大功能做一些其他工作。

使用Java进行配置的缺点是你要记住你的源码中有一些是既不属于业务，也不属于repository，而只是配置文件。

我们来看看之前的注解。文档在http://docs.spring.io/spring/docs/current/javadoc-api/ 

### 1.2.1 `@Configuration`注解

文档中关于`@Cofiguration`注解内容如下：

```
@Target(value=TYPE)
 @Retention(value=RUNTIME)
 @Documented
 @Component
public @interface Configuration
```

> Indicates that a class declares one or more @Bean methods and may be processed by the Spring container to generate bean definitions and service requests for those beans at runtime, for example:

```
@Configuration
public class AppConfig {
     @Bean
     public MyBean myBean() {
         // instantiate, configure and return bean ...
     }
 }
```

对于上面的`public MyBean myBean()`方法，在XML中`myBean`对应ID（每个Bean都有一个唯一的ID），`MyBean`则对应XML中的类型。也就是说我们有一个叫做`myBean`的Bean，类型是`MyBean`。

```
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

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

    @Bean
    public Team cubs() {
        return new Cubs();
    }
}
```

在我们的例子中，`game`这个Bean又调用了`redSox()`和`cubs()`，后面我们会详细解释Bean的范围。现在我们要知道一件事情就是，基本上，Spring会假设这些类都是单例的，也就是如果实例不存在，就实例化，然后放入Spring Context中，如果实例存在，就返回实例。

要启动一个Spring容器，我们使用：

```
ApplicationContext context = 
	new AnnotationConfigApplicationContext(AppConfig.class);
```

除此之外，我们还可以从Context中获取其他信息：

```
//获取Bean的数量
System.out.println("Bean的数量" + context.getBeanDefinitionCount());
//获取Bean的名称
for (String name :
        context.getBeanDefinitionNames()) {
    System.out.println(name );
}
```

我们的输出：

```
Bean的数量12
org.springframework.context.annotation.internalConfigurationAnnotationProcessor
org.springframework.context.annotation.internalAutowiredAnnotationProcessor
org.springframework.context.annotation.internalRequiredAnnotationProcessor
org.springframework.context.annotation.internalCommonAnnotationProcessor
org.springframework.context.event.internalEventListenerProcessor
org.springframework.context.event.internalEventListenerFactory
appConfig
org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor
org.springframework.context.annotation.ConfigurationClassPostProcessor.enhancedConfigurationProcessor
redSox
cubs
game
```

我们可以看到，除了自己的Bean之外，还有一些其他的基础设施Bean，此外`appConfig`本身也是一个Bean.

### 1.2.2 `@Import`和`@ImportResource`注解

与`@Cofiguration`能一起用的还有:

- `@Import` 可以从别的Config中导入配置
- `@ImportResource` 从非Java文件中导入配置，比如从XML文件中导入配置

这些能够用于项目模块比较多的情况下。

现在，我们新建一个类`InfrastructureConfig`，一般来说基础设置配置的是数据库连接池、或者其他数据源等。 但是现在我们需要修改`build.gradle`来添加`spring-jdbc`依赖：

```
compile group: 'org.springframework', name: 'spring-jdbc', version: '4.3.2.RELEASE'
```

`InfrastructureConfig`内容如下：

```
package lx.spring.core;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import javax.sql.DataSource;

@Configuration
public class InfrastructureConfig {
    @Bean
    public DataSource dataSource() {
        return new DriverManagerDataSource();
    }
}
```

现在，我们可以在`AppConfig`中使用`InfrastructureConfig`了：

```
@Configuration
@Import(InfrastructureConfig.class)
public class AppConfig {...}
```

现在假设我们的`BaseballGame`需要一个`DataSource`:

```
public class BaseballGame implements Game {
    private DataSource dataSource;

    public void setDataSource(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    //...
}    
```

现在，我们再修改`AppConfig`，让Bean自动配置`DataSource`:

```
@Bean
public Game game(DataSource dataSource) {
    BaseballGame game = new BaseballGame(redSox(), cubs());
    game.setDataSource(dataSource);
    return game;
}
```

这里，我们给`game()`这个Bean添加了参数`DataSource`，Spring会自动根据参数来查找对应的Bean（这里这个Bean在`InfrastructureConfig`中），然后产生一个参数，来进行配置。

以上就是用Java进行配置的方法，下面我们用基于注解的配置。

## 1.3 基于注解的配置

这一节我们需要接触两个概念：`Autowired`、`ComponentScan`和`Component`。

### 1.3.1 `@Autowired`注解

我们再来看看之前关于`game`的例子：

```
@Configuration
@Import(InfrastructureConfig.class)
public class AppConfig {
    @Bean
    public Game game(DataSource dataSource) {
        BaseballGame game = new BaseballGame(redSox(), cubs());
        game.setDataSource(dataSource);
        return game;
    }
    ...
}
```

这里，如果我们不知道`InfrastructureConfig`里的内容的话，`DataSouce`看起来有些奇怪，我们可以使用`Autowired`来寻找所有的Bean并且找到匹配的Bean:

```
import org.springframework.beans.factory.annotation.Autowired;
...
public class AppConfig {

    @Autowired //重点在这里
    private DataSource dataSource;
    @Bean
    public Game game() { //此处已经不需要参数了
        BaseballGame game = new BaseballGame(redSox(), cubs());
        game.setDataSource(dataSource); //直接引用
        return game;
    }
    ...
}    
```

注意：

1. `@Autowired`意味着自动按照**类型**触发，也就是**只有一个**这种**类型**的**Bean**时才起作用。
2. `@Autowired`对Bean的声明形式没有要求，可以是XML, Java或者使用`@Component`等声明。

我们之前系统里只有一个`DataSource`，所以这么做没问题，但是如果我们将这种方法用到`BaseballGame`身上，就会有问题。下面我们来看看如何解决这个问题。

### 1.3.2 `@ComponentScan`,`@Component` 以及`@Qualifier`注解

`@ComponentScan`会扫描当前文件夹及其子文件夹下，但是最好指定`basePackages`参数，告诉它从哪里开始扫描。 

现在我们再次修改`AppConfig`：

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

注意这里的`@Qualifier`就是前面说的如果存在多个相同类型的Bean时，如何让`@Autowired`能正常工作的解决方法。

另外，我们还需要对`RedSox`和`Cubs`类添加`@Component`注解：

```
@Component
public class RedSox implements Team {...}

@Component
public class Cubs implements Team {...}
```

使用注解声明的优点：我们不需要单独写Bean的配置，只需要声明就可以了。但这也有一些潜在的缺点：Bean可能散布在整个项目中，那我们需要大量的扫描，但是在现实中，我们一般会把Bean放到一个特殊的包中。一般来说，主要有下面几种常用的包：

- repositories
- services
- controllers

**注意**，扫描只在应用启动时进行一次，运行的时候是不会再扫描的。

### 1.3.3 `@Resource`注解

Java提供了一个标准的注解`@Resource`，也是用来自动触发，但与Spring的`Autowired`不同，它不是按照类型，而是**按照名字**触发。 

下面的例子是就是Spring根据变量的名字去找对应的Bean.

```
import javax.annotation.Resource;
...
public class AppConfig {
    @Resource
    private Team redSox;

    @Resource
    private Team cubs;

    @Bean
    public Game game() {
        BaseballGame game = new BaseballGame(redSox, cubs);
        ...
    }
}
```

但一般我们好少这么做，因为这样的话我们就没法改变量的名字了。

现在我们学习了使用Java配置、使用注解配置，接下来我们来看看使用XML进行配置，有点丑，但之前一直这么用，所以还是了解一下比较好。


## 1.4 基于XML的配置

对于基于注解的配置，我们必须要有源码才行。对于基于Java的配置，则是有些库根本就不支持这样的做法。XML配置有时会提供一些比较简便的配置方法，因此我们最好还是了解一下。 

### 1.4.1 XML配置声明
我们在`main/resources`下创建`applicationContext.xml`，注意，在IDEA中请用`new->XML Configuration File->Spring config`来新建。

为了方便，现在，我们先把`AppConfig`中的内容注释掉，把`RunDemo`的调用容器的也注释掉。

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
       
    <bean id="redSox" class="lx.spring.core.entities.RedSox"/>
    <bean id="cubs" class="lx.spring.core.entities.Cubs" />

    <bean id="game" class="lx.spring.core.entities.BaseballGame">
        <property name="awayTeam" ref="redSox" />
        <property name="homeTeam" ref="cubs" />
        <property name="dataSource" ref="dataSource" />
    </bean>

    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource"/>

</beans>
```

Bean由3部分组成，`id`, `class`和`property`。注意`property`可以使用`ref`来引用其他的Bean，也可以使用`value`来指定值。

### 1.4.2 加载XML配置

```
ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
```

**注意**，此时输出的Bean的数量只有4个（也就是XML中声明的数量）。

### 1.4.3 在XML配置中使用Bean扫描

我们要使用Bean扫描该怎么办呢？我们给XML添加命名空间就好了。

```
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
       http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/beans/spring-context.xsd">

    <context:component-scan base-package="lx.spring.core"/>
    ...
```  

这个时候除了`DataSource`需要声明外，其他的Bean不需要再XML文件中声明，只需要像之前那样使用注解声明就可以了。



