Title: Spring笔记05-Spring事务
Date: 2016-08-19 18:20
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 5.1 声明事务管理

ACID 属性:

Atomic原子：All or Nothing

Consistent一致性: DB integrity constraints never violated

Isolated: How transactions see work done by others

Durable: Committed changes are permanent

我们一般是在Service层声明事务。

### 5.1.1 @Transactional注解

`@Transactional`注解允许我们管理一个事务管理器。Spring不直接提供事务管理器，它提供一个hook连接到已经存在的事务管理器，比如我们使用关系数据库，那Spring就是使用关系数据库的事务管理器。Spring的Bean和Entity允许我们请求事务管理器，设置属性（比如超时、只读等）。

应用Spring的事务需要两步：

1. 应用`Transactional`注解
	a. XML格式
	b. 写代码的方式
2. 声明Platform Transaction Manager bean
3. `@EnableTransactionManagement`

`@Transactional`注解可以用于方法或者类。如果应用于一个类，那么这个类中的所有方法都应用了了这个注解，对于应用了类的注解，我们对单独的类重新使用`@Transactional`方法来覆盖。

`@Transactional`方法提供了一些参数让我们来指定一些配置。

### 5.1.2 PlatformTransactionManager接口

`PlatformTransactionManager`用来代表依赖平台的事务管理器。它的实现类有Hibernate, JPA等事务管理器。

### 5.1.3 案例：账户应用

首先，我们定义entity:

```
package lx.spring.core.entities;

import java.math.BigDecimal;

public class Account {
    private Long id;
    private BigDecimal balance;

    public Account() {
    }

    public Account(Long id, BigDecimal balance) {
        this.id = id;
        this.balance = balance;
    }
    // getter setter toString equals hashCode等
}
```

这里我们使用`BigDecimal`类型来表示余额。

然后我们创建一个Repository，用来表示数据库的操作：

```
package lx.spring.core.repositories;
import lx.spring.core.entities.Account;
import java.math.BigDecimal;
import java.util.List;

public interface AccountRepository {
    List<Account> getAccounts();
    Account getAccount(Long id);
    int getNumberOfCounts();
    Long createAccount(BigDecimal initialBalance);
    int deleteAccount(Long id);
    void updateAccount(Account account);
}
```

后面我们将看到如何用Spring Data来处理数据。

第一步，我们用`@Transactional` 实体。

```
@Service @Transactional
@Profile("test")
public class AccountService {
    @Autowired
    private AccountRepository repository;

    public BigDecimal getBalance(Long id) {
        return repository.getAccount(id).getBalance();
    }

    public BigDecimal deposit(Long id, BigDecimal amount) {
        Account account = repository.getAccount(id);
        BigDecimal newBalance = account.getBalance().add(amount);
        account.setBalance(newBalance);
        repository.updateAccount(account);
        return newBalance;
    }

    public BigDecimal withdraw(Long id, BigDecimal amount) {
        return deposit(id, amount.negate());
    }
}
```

第二步是声明Bean来适应事务，我们打开`AppConfig`：

```

@Configuration
@EnableTransactionManagement
public class AppConfig {

    @Bean(name = "transactionManager")
    @Profile("test")
    public PlatformTransactionManager transactionManagerForTest() {
        return new DataSourceTransactionManager(dataSourceForTest());
    }

    @Bean(name = "dataSource")
    @Profile("test")
    public DataSource dataSourceForTest() {
        DataSource dataSource = new BasicDataSource();
        return dataSource;
    }
}
```

注意这里的`transactionManagerForTest()`是返回事务管理器的，同时，我们用`EnableTransactionManagement`来指明我们的容器用了事务管理。

## 5.2 Isolation levels 隔离层级

`@Transactional`有一个属性是`Isolation`的，它是一个枚举，它有一下几个值：

- `DEFAULT` 使用底层数据存储所用的隔离层级
- `READ_COMMITTED` dirty reads are prevented; non-repeatable reads and phantom reads can occur.只有在其他事务提交时在一个事务里才能看到改变后的数据。如果事务需要很久，那就一直锁定直到事务完成。
- `READ_UNCOMMITTED` dirty reads, non-repeatable reads and phantom reads can occur. 两个事务同时对于一行操作。如果一个事务读取了行，然后又修改了数据，那另一个事务可以看到其他事务修改后的数据。这个好处是速度非常快，你没有锁定行，问题是它有一定风险，比如你根据没有提交的数据做了一些动作，但是这些改变又回滚了。所以，使用时需要分析一下。`READ_UNCOMMITTED`是最高效但不是最优的选择。
- `REPEATABLE_READ` dirty reads and non-repeatable reads are prevented; phantom reads can occur.如果数据修改后发生了回滚，则回滚前后我们看到的数据都是一样的
-  `SERIALIZABLE` dirty reads, non-repeatable reads and phantom reads are prevented 当我们查询或修改时，别的事务不能增加或修改表（整表锁定）

一般情况下我们不会修改这个属性。

## 5.3 Propagation 传播

Propagation说的是当事务执行时，我们的方法是否也是执行的。我们再回到`@Transactional`注解上来，它有一个属性是`Propagation`枚举。它有以下值：

- `MANDATORY`:  1. tx->join tx; 2. no->throw a TransactionRequiredException
- `NESTED`: 
- `NEVER`: 1. tx-> throw an exception; 2. no->nothing
- `NOT_SUPPORTED`: 1. tx->suspend tx; run outside tx; resume tx; 2. no->nothing
- `REQUIRED`：最常用，如果一个事务发生，那么我们加入，这是默认的；如果没有，我们创建一个事务。
- `REQUIRES_NEW`：1. tx1-> suspend tx1; create and run tx2; resume tx1; 2. no->create and run tx
- `SUPPORTS`: 1. tx->join tx; 2. no->nothing

除了`NESTED`,其它的都是来自于J2EE。

## 5.4 高效事务管理

Spring提供了两种高效高效管理事务的方法：

- 使用`TransactionTemplate`
- 使用`PlatfromTransactionManager`实现

### 5.4.1 TransactionTemplate

`TransactionTemplate`非常有用。我们在`Service`的构造方法里有提供`PlatformTransactionManager`参数：

```
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.TransactionStatus;
import org.springframework.transaction.support.TransactionTemplate;
import org.springframework.util.Assert;

public class SimpleService {
    private final TransactionTemplate transactionTemplate;

    public SimpleService(PlatformTransactionManager transactionManager) {
        Assert.notNull(transactionManager, "The 'transactionManager' argument must not be null");
        this.transactionTemplate = new TransactionTemplate(transactionManager);
    }

    public Object someServiceMethod() {
        return transactionTemplate.execute((TransactionStatus status) -> {
            System.out.println("hello");
            return "aaa";
        });
    }
    public void someServiceMethodWithouResult() {
        transactionTemplate.execute(new TransactionCallbackWithoutResult() {
            @Override
            protected void doInTransactionWithoutResult(TransactionStatus status) {
                System.out.println(status);
            }
        });
    }    
}
```

参数`TransactionStatus`有`setRollbackOnly()`方法，用于回滚事务。

我们可以在构造方法中设置事务：

```
public SimpleService(PlatformTransactionManager transactionManager) {
    Assert.notNull(transactionManager, "The 'transactionManager' argument must not be null");
    this.transactionTemplate = new TransactionTemplate(transactionManager);
    //这里我们可以设置会话
    this.transactionTemplate.setIsolationLevel(TransactionDefinition.ISOLATION_READ_UNCOMMITTED);
    this.transactionTemplate.setTimeout(30); //30秒
}
```

### 5.4.2 PlatformTransactionManager

我们也可以使用`org.springframework.transaction.PlatformTransactionManager`直接管理会话。将`PlatformTransactionManager`直接传给bean，然后可以使用`TransactionDefinistion`和`TransactionStatus`来初始化会话、回滚、提交等。

在`TransactionTemplate`和`PlatformTransactionManager`之间有限推荐第一个。






