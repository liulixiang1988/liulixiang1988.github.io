Title: Spring笔记06-使用JdbcTemplate进行数据访问
Date: 2016-08-23 21:22
Category: Java
Tags: Java, Spring
Author: 刘理想

[TOC]

## 6.1 实例化和配置

没有Spring的情况下，JDBC非常难用；有了Spring，JDBC就变得比较靠谱可用了。

这里我们在Bean中使用`DataSource`对`JdbcTemplate`进行初始化。

```

@Repository
@Profile("test")
public class JdbcAccountRepository implements AccountRepository {

    private JdbcTemplate template;
    private static long nextId = 4;

    @Autowired
    public JdbcAccountRepository(DataSource dataSource) {
        template = new JdbcTemplate(dataSource);
    }
}
```

后面我们看如何配置多个`DataSource`。

## 6.2 定义数据源Data Source

之前我们已经讲了`@Component`，但是在实际中我们更多的遇到的是`@Service`和`@Repository`，`@Controller`，这些其实也是`@Component`。

首先我们来添加一些依赖：

```
dependencies {
    compile group: 'org.apache.commons', name: 'commons-dbcp2', version: '2.1.1'

    compile group: 'org.springframework', name: 'spring-test', version: '4.3.2.RELEASE'
    compile group: 'org.hamcrest', name: 'hamcrest-library', version: '1.3'
	//...

    return'com.h2database:h2:1.4.192'

}
```

注意这里我们用了返回，这是因为需要对应的驱动

我们修改`AppConfig.java`：

```
@Bean(name = "dataSource", destroyMethod = "shutdown")
@Profile("test")
public DataSource dataSourceForTest() {
    return new EmbeddedDatabaseBuilder()
            .generateUniqueName(true)
            .setType(EmbeddedDatabaseType.H2)
            .setScriptEncoding("UTF-8")
            .ignoreFailedDrops(true)
            .addScript("schema.sql")
            .addScripts("data.sql")
            .build();
}

@Bean(name = "dataSource")
@Profile("prod")
public DataSource dataSourceForProd() {
    BasicDataSource dataSource = new BasicDataSource();
    dataSource.setDriverClassName(env.getProperty("db.driver"));
    dataSource.setUrl(env.getProperty("db.url"));
    dataSource.setUsername(env.getProperty("db.user"));
    dataSource.setPassword(env.getProperty("db.pass"));
    return dataSource;
}
```


## 6.3 Profile注解和Environment

在项目中，我们需要多个环境（开发、测试、产品）等，每个环境对应的数据库也不同。Spring能通过`@Profile`来给不同的环境提供不同的配置Bean但是有相同的名称。

`@Profile`可以放在配置类上，也可以放在Bean的方法上，如果用在Bean方法上，需要给`@Bean`提供`name`属性，这样可以让不同的Bean有相同的名称。

```
@Configuration
@ComponentScan(basePackages = "lx.spring.core")
@PropertySource("classpath:prod.properties")
@EnableTransactionManagement
public class AppConfig {

    @Autowired
    private Environment env;

    @Bean(name = "dataSource", destroyMethod = "shutdown")
    @Profile("test")
    public DataSource dataSourceForTest() {
        return new EmbeddedDatabaseBuilder()
                .generateUniqueName(true)
                .setType(EmbeddedDatabaseType.H2)
                .setScriptEncoding("UTF-8")
                .ignoreFailedDrops(true)
                .addScript("schema.sql")
                .addScript("data.sql")
                .build();
    }

    @Bean(name = "transactionManager")
    @Profile("test")
    public PlatformTransactionManager transactionManagerForTest() {
        return new DataSourceTransactionManager(dataSourceForTest());
    }

    @Bean(name = "dataSource")
    @Profile("prod")
    public DataSource dataSourceForProd() {
        BasicDataSource dataSource = new BasicDataSource();
        dataSource.setDriverClassName(env.getProperty("db.driver"));
        dataSource.setUrl(env.getProperty("db.url"));
        dataSource.setUserName(env.getProperty("db.user"));
        dataSource.setPassword(env.getProperty("db.pass"));
        return dataSource;
    }

    @Bean(name = "transactionManager")
    @Profile("prod")
    public PlatformTransactionManager transactionManagerForProd() {
        return new DataSourceTransactionManager(dataSourceForProd());
    }
}
```

这里我们看到`@Profile`提供了不同环境下的使用方式。同时需要注意一点是我们使用了
`@PropertySource("classpath:prod.properties")`这样的注解，它会在`resources`下寻找`prod.properties`这个文件，然后使用了：

```
@Autowired
private Environment env;
```

接着就能读取变量了：

```
env.getProperty("db.url")
```

## 6.4 实现CRUD方法

```
@Repository
@Profile("test")
public class JdbcAccountRepository implements AccountRepository {

    private JdbcTemplate template;
    private static long nextId = 4;

    @Autowired
    public JdbcAccountRepository(DataSource dataSource) {
        template = new JdbcTemplate(dataSource);
    }

    @Override
    public List<Account> getAccounts() {
        String sqlText = "SELECT * FROM account";
        return template.query(sqlText, new AccountMapper());
    }

    @Override
    public Account getAccount(Long id) {
        String sqlText = "SELECT * FROM account WHERE id=?";
        return template.queryForObject(sqlText, new AccountMapper(), id);
    }

    @Override
    public int getNumberOfCounts() {
        String sqlText = "SELECT count(*) FROM account";
        return template.queryForObject(sqlText, Integer.class);
    }

    @Override
    public Long createAccount(BigDecimal initialBalance) {
        String sqlText = "insert into account(id, balance) values(?, ?)";
        long id = nextId ++;
        int uc = template.update(sqlText, id, initialBalance);
        return id;
    }

    @Override
    public int deleteAccount(Long id) {
        String sqlText = "delete from account where id=?";
        return template.update(sqlText, id);
    }

    @Override
    public void updateAccount(Account account) {
        String sqlText = "update account set balance=? where id = ?";
        template.update(sqlText, account.getBalance(), account.getId());
    }

    private class AccountMapper implements RowMapper<Account>{
        @Override
        public Account mapRow(ResultSet rs, int rowNum) throws SQLException {
            return new Account(rs.getLong("id"), rs.getBigDecimal("balance"));
        }
    }
}
```

注意，我们需要提供实现`RowMapper`来返回对象类型。

## 6.5 测试Repository

IDEA的打开最近文件、窗口的快捷键：`Cmd+E`.

我们创建`JdbcAccountRepositoryTest.java`：

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppConfig.class)
@Transactional
@ActiveProfiles("test")
public class JdbcAccountRepositoryTest {

    @Autowired
    private AccountRepository repository;

    @Test
    public void getAccounts() throws Exception {
        List<Account> accounts = repository.getAccounts();
        //1. 比较
        assertThat(accounts.size(), is(3));
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
    }
}
```

注意里面的`assertThat`, `is`, `closeTo`等。

现在我们再来看`AccountService`,它有事务：

```
@Service @Transactional
@Profile("test")
public class AccountService {
    @Autowired
    private AccountRepository repository;

    @Transactional(readOnly = true, propagation = Propagation.SUPPORTS)
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

    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        withdraw(fromId, amount);
        deposit(toId, amount);
    }
}
```

然后我们看看对应的测试：

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = AppConfig.class)
@Transactional
@ActiveProfiles("test")
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

这里事务运行的没问题。



