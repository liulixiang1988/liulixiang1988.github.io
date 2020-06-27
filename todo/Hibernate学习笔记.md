Title: Hibernate学习笔记
Date: 2016-05-09 13:52
Category: Java
Tags: Java, Hibernate
Author: 刘理想

[TOC]

##1. Java链接数据库

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import java.util.List;
import java.util.ArrayList;

public class JdbcMain {
  public static void main(String[] args) throws ClassNotFoundException {
    // TODO: Load the SQLite JDBC driver (JDBC class implements java.sql.Driver)
    Class.forName("org.sqlite.JDBC");
    // TODO: Create a DB connection
    try(Connection connection = DriverManager.getConnection("jdbc:sqlite:contactmgr.db")) {
      
      // TODO: Create a SQL statement
      Statement statement = connection.createStatement();
      
      // TODO: Create a DB table
      statement.executeUpdate("DROP TABLE IF EXISTS contacts");
      statement.executeUpdate("CREATE TABLE contacts (id INTEGER PRIMARY KEY, firstname STRING, lastname STRING, email STRING, phone INT(10))");
      
      // TODO: Insert a couple contacts
      statement.executeUpdate("INSERT INTO contacts (firstname, lastname, email, phone) VALUES('Liu', 'Lixiang', 'liulixiang1988@gmail.com', 1234567890)");
      statement.executeUpdate("INSERT INTO contacts (firstname, lastname, email, phone) VALUES('Long', 'Long', 'abc@gmail.com', 0987654321)");
      
      // TODO: Fetch all the records from the contacts table
      ResultSet rs = statement.executeQuery("SELECT * FROM contacts");
      
      // TODO: Iterate over the ResultSet & display contact info
      while(rs.next()){
        int id = rs.getInt("id");
        String firstName = rs.getString("firstname");
        String lastName = rs.getString("lastname");
        
        System.out.printf("%s %s (%d)", firstName, lastName, id);
      }
      
    } catch (SQLException ex) {
      // Display connection or query errors
      System.err.printf("There was a database error: %s%n",ex.getMessage());
    }
  }
}
```

编译：`javac JdbcMain.java`

运行：`java -cp sqlite-jdbc.3.8.6.jar:. JdbcMain`

##2 Hibernate入门

###2.1 创建一个使用Hibernate的项目

创建gradle项目`contactmgr-hibernate`项目,并修改`build.gradle`:

```
dependencies {
    compile 'org.hibernate:hibernate-core:5.1.0.Final'
    compile 'com.h2database:h2:1.4.191'
}
```

在根目录创建`data`文件夹，用来存放h2文件数据库。

在`resources`目录下创建`hibernate.cfg.xml`，这个文件用来保存`SessionFactory`的配置：

```xml
<hibernate-configuration>
    <session-factory>
        <!--数据库链接配置-->
        <property name="connection.driver_class">org.h2.Driver</property>
        <property name="connection.url">jdbc:h2:./data/contactmgr</property>
        <!--SQL 方言-->
        <property name="dialect">org.hibernate.dialect.H2Dialect</property>
    </session-factory>
</hibernate-configuration>
```

###2.2 使用JPA注解添加POJO

添加包`io.github.liulixiang1988.contactmgr`，并在其中创建`Application`类：

```java
package io.github.liulixiang1988.contactmgr;

public class Application {
    public static void main(String[] args) {

    }
}
```

然后在`contactmgr`中创建`model`包，并创建`Contact`类：

```java
package io.github.liulixiang1988.contactmgr.model;

import javax.persistence.*;

@Entity //映射为表
public class Contact {

    @Id //主键
    @GeneratedValue(strategy = GenerationType.IDENTITY) //自增长
    private int id;

    @Column
    private String firstName;

    @Column
    private String lastName;

    @Column
    private String email;

    @Column
    private Long phone;

    // 为JPA准备的
    public Contact(){}

    @Override
    public String toString() {
        return "Contact{" +
                "id=" + id +
                ", firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", email='" + email + '\'' +
                ", phone=" + phone +
                '}';
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Long getPhone() {
        return phone;
    }

    public void setPhone(Long phone) {
        this.phone = phone;
    }
}
```

然后我们修改`hibernate.cfg.xml`来为model做一些配置：

```xml
<hibernate-configuration>
    <session-factory>
        <!--...-->
        <!--启动时创建数据结构-->
        <property name="hbm2ddl.auto">create</property>

        <!--为标识的实体类命名-->
        <mapping class="io.github.liulixiang1988.contactmgr.model.Contact"/>
    </session-factory>
</hibernate-configuration>
```

###2.3 创建Hibernate SessionFactory

```
public class Application {
    //保存一个SessionFactory(我们也只需要一个)
    private static final SessionFactory sessionFactory = buildSessionFactory();

    private static SessionFactory buildSessionFactory() {
        //创建StandardServiceRegistry
        final ServiceRegistry registry = new StandardServiceRegistryBuilder().configure().build();
        return new MetadataSources(registry).buildMetadata().buildSessionFactory();
    }

    public static void main(String[] args) {

    }
}
```

###2.4 Builder模式

修改Contact.java

```
public class Contact {
    
    //...

    @Override
    public String toString() {
        return "Contact{" +
                "id=" + id +
                ", firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", email='" + email + '\'' +
                ", phone=" + phone +
                '}';
    }

    //...

    public static class ContactBuilder {
        private String firstName;
        private String lastName;
        private String email;
        private Long phone;

        public ContactBuilder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }

        public ContactBuilder withEmail(String email) {
            this.email = email;
            return this;
        }

        public ContactBuilder withPhone(Long phone) {
            this.phone = phone;
            return this;
        }

        public Contact build() {
            return new Contact(this);
        }
    }
}
```

修改`Application.java`:

```
public static void main(String[] args) {
    Contact contact = new ContactBuilder("Chris","Ramacciotti")
            .withEmail("rama@teamtreehouse.com")
            .withPhone(7735556666L)
            .build();
}
```

##3. 使用Hibernate存取数据

要让Hibernate支持存取数据，需要添加依赖：

```
compile 'javax.transaction:jta:1.1'
```

为了能看到Hibernate准备的查询，我们设置：

```
<property name="show_sql">true</property>
```

###3.1 添加数据

```
public static void main(String[] args) {
    Contact contact = new ContactBuilder("Chris","Ramacciotti")
            .withEmail("rama@teamtreehouse.com")
            .withPhone(7735556666L)
            .build();

    //打开session
    Session session = sessionFactory.openSession();

    //begin transaction
    session.beginTransaction();

    //使用session来保存对象
    session.save(contact);

    //提交session
    session.getTransaction().commit();

    //关闭session
    session.close();
}
```

###3.2 查询数据

```
@SuppressWarnings("unchecked")
private static List<Contact> fetchAllContact() {
    Session session = sessionFactory.openSession();
    //创建Criteria
    Criteria criteria = session.createCriteria(Contact.class);

    //从Criteria中获取数据
    List<Contact> contacts = criteria.list();

    //关闭会话
    session.close();
    return contacts;
}
```

调用可以使用java8的特性，而非使用for循环：
```
fetchAllContact().stream().forEach(System.out::println);
```

可以通过修改`hibernate.cfg.xml`，让每次启动时是更新而不是创建数据库：

```
<property name="hbm2ddl.auto">update</property>
```

###3.3 更新与删除

```
private static Contact findContactById(int id) {
    //打开会话
    Session session = sessionFactory.openSession();
    //获取对象或者null
    Contact contact = session.get(Contact.class, id);
    //关闭会话
    session.close();
    //返回对象
    return contact;
}

private static void update(Contact contact) {
    Session session = sessionFactory.openSession();
    session.beginTransaction();
    session.update(contact);
    session.getTransaction().commit();
    session.close();
}

private static void delete(Contact contact) {
    Session session = sessionFactory.openSession();
    session.beginTransaction();
    session.delete(contact);
    session.getTransaction().commit();
    session.close();
}
```


