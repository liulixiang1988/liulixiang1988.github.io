Title: Spring笔记04-使用Spring进行测试
Date: 2016-08-12 17:22
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 4.1 标准测试注解和injecting fixture

Spring的一个特点是测试Component非常方便。

现在我们为`BaseballGame`创建`BaseballGameTest`。然后做测试：

```
public class BaseballGameTest {
    private Game game;
    private ApplicationContext ctx;

    @Before
    public void setUp() throws Exception {
        ctx = new AnnotationConfigApplicationContext(AppConfig.class);
        game = ctx.getBean("game", Game.class);
    }

    @Test
    public void playGame() throws Exception {
        String home = game.getHomeTeam().getName();
        String away = game.getAwayTeam().getName();

        String result = game.playGame();

        assertTrue(result.contains(home) || result.contains(away));
    }

    @Test
    public void testGetAndSetHomeTeam() throws Exception {
        Team royals = ctx.getBean("royals", Team.class);
        game.setHomeTeam(royals);
        assertEquals(royals.getName(), game.getHomeTeam().getName());
    }

}
```

这里每次测试的时候，都要调用一次`setUp`，也就是我们的`ApplicationContext`会被反复创建很多次，Spring提供了一些方法让`ApplicationContext`只执行一次。

```
@RunWith(SpringJUnit4ClassRunner.class) //
@ContextConfiguration(classes = AppConfig.class) //
public class BaseballGameTest {
    @Autowired //
    private Game game;

    @Autowired //
    private ApplicationContext ctx;

    @Test
    public void playGame() throws Exception {
        String home = game.getHomeTeam().getName();
        String away = game.getAwayTeam().getName();

        String result = game.playGame();

        assertTrue(result.contains(home) || result.contains(away));
    }

    @Test
    public void testGetAndSetHomeTeam() throws Exception {
        Team royals = ctx.getBean("royals", Team.class);
        game.setHomeTeam(royals);
        assertEquals(royals.getName(), game.getHomeTeam().getName());
    }
}
```

这里我们用`@RunWith(SpringJUnit4ClassRunner.class)`和`@ContextConfiguration(classes = AppConfig.class)`让我们可以直接用Bean和`ApplicationContext`。

## 4.2 事务测试

事务测试是当测试完成后，所有的测试操作都会回滚。我们用`@Transactional`来表示事务测试。

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppConfig.class)
@Transactional
public class BaseballGameTest { ... }
```

还有`@Commit`的注解，表示会提交；`@Rollback`表示回滚；`@AfterTransaction`表示事务执行后再执行。

还有就是`@DirtiesContext`表示测试上下文会影响到应用中的上下文，当测试执行后重新加载应用的上下文。

我们看到Spring的事务测试和回滚机制非常棒，即使我们不用Spring也可以把它拿出来用于测试。