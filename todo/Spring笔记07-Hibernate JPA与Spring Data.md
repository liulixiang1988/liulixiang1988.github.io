Title: Spring笔记07-Hibernate JPA与Spring Data
Date: 2016-08-24 18:51
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 7.1 映射JPA实体

JPA: Java Persistence API 它是从EJB3中抽取出来的。

- `EntityManagerFactory`
- `EntityManager`
- `persistence.xml`
- `@Entity`
- `@Column`
- `@Table`


要使用JPA，我们需要添加依赖：

```
compile group: 'javax', name: 'javaee-api', version: '7.0'
```

我们来新建数据：

```
@Entity
public class Account {
    @Id
    private Long id;
    private BigDecimal balance;

   //...
}
```

然后配置数据源：

```
@Configuration
@ComponentScan(basePackages = "lx.spring.core")
@PropertySource("classpath:prod.properties")
@EnableTransactionManagement
public class AppConfig {

    @Autowired
    private Environment env;

    @Bean
    public DataSource dataSource() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName(env.getProperty("db.driver"));
        dataSource.setUrl(env.getProperty("db.url"));
        dataSource.setUsername(env.getProperty("db.user"));
        dataSource.setPassword(env.getProperty("db.pass"));
        return dataSource;
    }
}
```

## 7.2 Entity Manager和Vendor Adapter

首先我们先添加依赖：

```
dependencies {
    compile group: 'org.springframework', name: 'spring-jdbc', version: '4.3.2.RELEASE' //1. jdbc
    compile group: 'org.springframework', name: 'spring-orm', version: '4.3.2.RELEASE' //2. orm
    compile group: 'org.apache.commons', name: 'commons-dbcp2', version: '2.1.1'  //3. dbcp
    compile group: 'org.hibernate', name: 'hibernate-core', version: '5.2.2.Final' //4. hibernate
	//...

    compile 'com.h2database:h2:1.4.192' //5. h2驱动
    compile group: 'mysql', name: 'mysql-connector-java', version: '6.0.3' //6. mysql驱动

}
```


现在我们来修改`AppConfig`：

```
@Bean
public JpaVendorAdapter jpaVendorAdapter() {
    HibernateJpaVendorAdapter adapter = new HibernateJpaVendorAdapter();
    adapter.setShowSql(true);
    adapter.setGenerateDdl(true);
    adapter.setDatabase(Database.MYSQL);
    return adapter;
}

@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory (
        DataSource dataSource, JpaVendorAdapter jpaVendorAdapter
) {
    Properties props = new Properties();
    props.setProperty("hibernate.format_sql", String.valueOf(true));

    LocalContainerEntityManagerFactoryBean emf = new LocalContainerEntityManagerFactoryBean();
    emf.setDataSource(dataSource);
    emf.setPackagesToScan("lx.spring.core.entities");
    emf.setJpaVendorAdapter(jpaVendorAdapter);
    emf.setJpaProperties(props);

    return emf;
}

@Bean
public PlatformTransactionManager transactionManager(EntityManagerFactory emf) {
    return new JpaTransactionManager(emf);
}

@Bean
public BeanPostProcessor persistenceTranslation() {
    return new PersistenceAnnotationBeanPostProcessor();
}
```

## 7.3 实现JPA Repository

现在我们用EntityManagerFactory来实现JPA Repository。

我们来创建`AccountRepository`接口和`JpaAccountRepository`类：

```
package lx.spring.core.repositories;

import lx.spring.core.entities.Account;

import java.math.BigDecimal;
import java.util.List;

public interface AccountRepository {
    List<Account> getAccounts();

    Account getAccount(Long id);

    int getNumberOfAccounts();

    Long createAccount(BigDecimal initialBalance);

    int deleteAccount(Long id);

    void updateAccount(Account account);
}
```

```
package lx.spring.core.repositories;

import lx.spring.core.entities.Account;
import org.springframework.stereotype.Repository;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import java.math.BigDecimal;
import java.util.List;

@Repository
public class JpaAccountRepository implements AccountRepository {
    private long nextId = 4;

    @PersistenceContext //Spring会注入
    private EntityManager entityManager;

    @Override
    public List<Account> getAccounts() {
        return entityManager.createQuery("select a from Account a", Account.class).getResultList();
    }

    @Override
    public Account getAccount(Long id) {
        return entityManager.find(Account.class, id);
    }

    @Override
    public int getNumberOfAccounts() {
        String jpaText = "select count(a.id) from Account a";
        Long result = (Long) entityManager.createQuery(jpaText).getSingleResult();
        return result.intValue();
    }

    @Override
    public Long createAccount(BigDecimal initialBalance) {
        long id = nextId++;
        entityManager.persist(new Account(id, initialBalance));
        return id;
    }

    @Override
    public int deleteAccount(Long id) {
        entityManager.remove(getAccount(id));
        return 1;
    }

    @Override
    public void updateAccount(Account account) {
        entityManager.merge(account);
    }
}
```

这里我们用的不是标准的SQL，而是JPA表达式。而且像`select count(a.id) from Account a`这些语句里都是类名和字段名，所以是区分大小写的。

## 7.4 测试

我们创建的测试和上一节类似：

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=AppConfig.class)
@Transactional
public class JpaAccountRepositoryTest {
    @Autowired
    private AccountRepository repository;

    @Test
    public void getAccounts() throws Exception {
        List<Account> accounts = repository.getAccounts();
        assertThat(accounts.size(), is(3));
    }

    @Test
    public void getNumberOfAccounts() throws Exception {
        assertThat(repository.getNumberOfAccounts(), is(3));
    }

    @Test
    public void getAccount() throws Exception {
        Account account = repository.getAccount(1L);
        assertThat(account.getId(), is(1L));
        //2. 近似
        assertThat(new BigDecimal("100.0"), is(closeTo(account.getBalance(), new BigDecimal("0.01"))));
    }

    @Test
    public void createAccount() throws Exception {
        Long id = repository.createAccount(new BigDecimal("250.00"));
        //3. 非空
        assertThat(id, is(notNullValue()));

        Account account = repository.getAccount(id);
        assertThat(account.getId(), is(id));
        assertThat(new BigDecimal("250.0"), is(closeTo(account.getBalance(), new BigDecimal("0.01"))));
    }

    @Test
    public void updateAccount() throws Exception {
        Account account = repository.getAccount(1L);
        BigDecimal current = account.getBalance();
        BigDecimal amount = new BigDecimal("50.0");
        account.setBalance(current.add(amount));
        repository.updateAccount(account);

        Account again = repository.getAccount(1L);
        assertThat(again.getBalance(), is(closeTo(account.getBalance(), new BigDecimal("0.01"))));
    }

    @Test
    public void deleteAccount() throws Exception {
        for (Account account: repository.getAccounts()) {
            repository.deleteAccount(account.getId());
        }
        assertThat(repository.getNumberOfAccounts(), is(0));
    }

}
```

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppConfig.class)
@Transactional
public class AccountServiceTest {

    @Autowired
    private AccountService service;

    @Test
    public void deposit() throws Exception {
        BigDecimal start = service.getBalance(1L);
        BigDecimal amount = new BigDecimal("50.0");

        service.deposit(1L, amount);
        BigDecimal finish = start.add(amount);

        assertThat(finish, is(closeTo(service.getBalance(1L), new BigDecimal("0.01"))));
    }

    @Test
    public void withdraw() throws Exception {
        BigDecimal start = service.getBalance(1L);
        BigDecimal amount = new BigDecimal("50.0");

        service.withdraw(1L, amount);
        BigDecimal finish = start.subtract(amount);

        assertThat(finish, is(closeTo(service.getBalance(1L), new BigDecimal("0.01"))));
    }

    @Test
    public void transfer() throws Exception {
        BigDecimal acct1start = service.getBalance(1L);
        BigDecimal acct2start = service.getBalance(2L);

        BigDecimal amount = new BigDecimal("50.0");
        service.transfer(1L, 2L, amount);
        BigDecimal acct1finish = acct1start.subtract(amount);
        BigDecimal acct2finish = acct2start.add(amount);

        assertThat(acct1finish, is(closeTo(service.getBalance(1L), new BigDecimal("0.01"))));
        assertThat(acct2finish, is(closeTo(service.getBalance(2L), new BigDecimal("0.01"))));
    }
}
```

## 7.5 Spring Data JPA

Spring Data JPA能够进一步简化我们的应用。首先我们添加依赖：

```
compile group: 'org.springframework.data', name: 'spring-data-jpa', version: '1.10.2.RELEASE'
```

然后我们修改`AppConfig.java`

```
@Configuration
@ComponentScan(basePackages = "lx.spring.core")
@EnableJpaRepositories(basePackages = "lx.spring.core.repositories") //这个注解是用来发现spring data jpa的
@PropertySource("classpath:prod.properties")
@EnableTransactionManagement
public class AppConfig {...}
```

注意`@EnableJpaRepositories`注解。

接着，我们修改`AccountRepository`并删除对应的实现类：

```
public interface AccountRepository extends JpaRepository<Account, Long>{
    List<Account> findAccountsByBalanceGreaterThanEqual(BigDecimal amount);
}
```

注意这里我们继承了`JpaRepository`，这样就可以了。不需要实现什么。

同时，我们定义了`findAccountsByBalanceGreaterThanEqual`方法，这个名称是约定的，`find`表示查找，`By`后面的是字段，`GreaterThanEqual`是条件。

此时，我们在测试中调用：

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=AppConfig.class)
@Transactional
public class JpaAccountRepositoryTest {
    @Autowired
    private AccountRepository repository;

    @Test
    public void getAccounts() throws Exception {
        List<Account> accounts = repository.findAll();
        assertThat(accounts.size(), is(3));
    }

    @Test
    public void getNumberOfAccounts() throws Exception {
        assertThat(repository.count(), is(3));
    }

    @Test
    public void getAccount() throws Exception {
        Account account = repository.findOne(1L);
        assertThat(account.getId(), is(1L));
        //2. 近似
        assertThat(new BigDecimal("100.0"), is(closeTo(account.getBalance(), new BigDecimal("0.01"))));
    }

    @Test
    public void createAccount() throws Exception {
        Account account = new Account(99L, new BigDecimal("250.00"));
        repository.save(account);
        Long id = account.getId();
        //3. 非空
        assertThat(id, is(notNullValue()));

        Account again = repository.findOne(id);
        assertThat(again.getId(), is(id));
        assertThat(new BigDecimal("250.0"), is(closeTo(account.getBalance(), new BigDecimal("0.01"))));
    }

    @Test
    public void updateAccount() throws Exception {
        Account account = repository.findOne(1L);
        BigDecimal current = account.getBalance();
        BigDecimal amount = new BigDecimal("50.0");
        account.setBalance(current.add(amount));
        repository.save(account);

        Account again = repository.findOne(1L);
        assertThat(again.getBalance(), is(closeTo(account.getBalance(), new BigDecimal("0.01"))));
    }

    @Test
    public void deleteAccount() throws Exception {
        repository.deleteAll();
        assertThat(repository.count(), is(0L));
    }

}
```

可以看到只是方法名变了，怎么样，是不是很简单？