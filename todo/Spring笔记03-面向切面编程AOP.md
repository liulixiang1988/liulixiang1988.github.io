Title: Spring笔记03-面向切面编程AOP
Date: 2016-08-12 16:15
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 3.1 AOP的概念

AOP适用于以下场景：代码交织(code tangling)和代码分散(code scattering)

代码交织是指一个方法完成多个功能，这几个功能之间又没有必然联系，比如我们需要做日志、检查授权和执行业务逻辑，如果我们不用AOP的话那这三个就混在一起了。

代码分散式指比如我们有多处需要做日志，然后这些代码在多个地方重复。

AOP允许我们将这些动作封装成一个类，然后告诉Spring我们希望在多个地方调用这个类（成为Aspect），从而避免代码交织和分散。 

AOP会设计到一下几个概念：

- 连接点Join Point
- 切入点Point Cut
- Advice
- Aspect
- Weaving

### 3.1.1 连接点(Join Point)和切入点(Point Cut)

连接点，指的是我们能够应用我们Aspect的地方。这是告诉我们where to use the Aspects.
在一般概念的AOP上，我们可以在任何地方用Aspect，但是Spring对这一规则做了限制，我们只能用在Spring管理的Bean的公共方法中。

切入点一般来说可以认为和连接点类似。表示我们实际声明的连接点。

### 3.1.2 Advice

Advice指的是我们想应用的方法(The functionality we want to apply)，比如是Before, After等。

### 3.1.3 Aspect

Aspect是Point Cut和Advice的结合，也就是我们什么时候做、做什么。

### 3.1.4 Weaving

将Aspect应用到系统的过程(The process of applying an aspect to our system)

好了，在将AOP应用到我们前面讲到的例子之前，我们先添加一些依赖：

```
compile group: 'org.springframework', name: 'spring-aop', version: '4.3.2.RELEASE'
compile group: 'aopalliance', name: 'aopalliance', version: '1.0'
compile group: 'org.aspectj', name: 'aspectjweaver', version: '1.8.9'
```

`spring-aop`底层是调用`aspectj`，但是有时候我们会直接使用`aspectj`因此这里还是直接添加一下依赖。

## 3.2 定义切入点和应用Advice

这一节我们对之前的例子应用AOP。我们对`Game`做记录，记录下每个方法的调用，什么时候添加`setHomeTeam`, `setAwayTeam`等。

首先，我们需要在Bean配置里添加注解`@EnableAspectJAutoProxy`。这个注解表示Spring使用AspectJ的注解代理。

```
@Configuration
@ComponentScan(basePackages = "lx.spring.core")
@EnableAspectJAutoProxy
public class AppConfig {...}
```

第二部，我们新建一个`aspects`包，并在包下面创建`LoggingAspect`类:

```
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

import java.util.logging.Logger;

@Aspect
@Component //这样使用起来方便
public class LoggingAspect {
    private Logger logger = Logger.getLogger(getClass().getName());
    @Before("execution(void lx.spring.core..*.set*(*))") //指定Point Cut
    public void callSetters() {
        logger.info("Setters called!");
    }
}
```

这里，我们创建了类，并用`@Aspect`标示它是一个Aspect。一个Aspect由切入点和Advice组成，`@Before`指定了切入点，也就是匹配所有`lx.spring.core`包及其下面的包中的`set`开头只有一个参数返回类型是`void`类型的方法。`callSetters()`方法是advice。

因为我们给Aspect加了`@Component`，加之我们在配置时用了`@ComponentScan`，所以现在我们什么都不用做，就会自动的调用这个方法了。是不是很神奇？

```
Team royals = context.getBean("royals", Team.class);
Team redSox = context.getBean("redSox", Team.class);
Team cubs = context.getBean("cubs", Team.class);

Game game1 = context.getBean("game", Game.class);
System.out.println(game1);
game1.setHomeTeam(royals);
game1.setAwayTeam(redSox);
//输出
//Game between Bostom Red Sox at Chicago Cubs
//八月 12, 2016 10:24:44 上午 lx.spring.core.aspects.LoggingAspect callSetters
//信息: Setters called!
//八月 12, 2016 10:24:44 上午 lx.spring.core.aspects.LoggingAspect callSetters
//信息: Setters called!
```

其实，我们的`callSetters()`方法可以添加`JoinPoint`参数，用来提供一些调用信息，这样大大提高了AOP的功能，比如：

```
@Before("execution(void lx.spring.core..*.set*(*))") //指定Point Cut
public void callSetters(JoinPoint joinPoint) {
    //获取方法名
    String method = joinPoint.getSignature().getName();
    //获取参数
    String args = Arrays.stream(joinPoint.getArgs()).map(Object::toString).collect(Collectors.joining(", "));
    //获取target，也就是所属的类
    Object target = joinPoint.getTarget();
    logger.info("在"+target+"上调用"+method+"参数:"+args);
}
```

## 3.3 Advice的类型

Advice包含很多内容，请查看Spring文档的AspectJ部分。这里举一个例子：

```
@Around("execution(String playGame())")
public Object checkForRain(ProceedingJoinPoint pjp) throws Throwable {
    boolean rain = Math.random() < 0.5;
    Object result = null;
    if (rain) {
        logger.info(pjp.getTarget() + " rained out");
    } else {
        result = pjp.proceed();
        logger.info(result.toString());
    }
    return result;
}
```

这个是在执行前和执行后都会执行，其中`ProceedingJoinPoint.proceed()`会调用对应的方法。