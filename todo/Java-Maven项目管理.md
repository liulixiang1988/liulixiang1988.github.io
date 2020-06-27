Title: Maven项目管理
Date: 2016-06-21 20:00
Category: Java
Tags: Maven
Author: 刘理想

[TOC]

## maven快速入门

Maven是基于项目对象模型(POM)，可以通过一小段描述信息来管理项目的构建、报告和文档的软件项目管理工具。

### maven安装配置
下载好maven后，需要配置环境变量。

1. 设置`JAVA_HOME`：`D:\program files\Java\jdk1.8.0_91`, path添加`%JAVA_HOME%\bin`
2. 新建`M2_HOME`,指向maven安装目录：`D:\app\apache-maven-3.3.9`
3. 修改path，添加`%M2_HOME%\bin`
4. 在命令行中输入`mvn -v`看看是否输出正确。

### maven helloworld案例

maven目录结构结构

```
src
    -main
        -java
            -package
    -test
        -java
            -package
    -resources
```

按照上面的目录结构创建目录，package目录先不要创建package。

分别新建
```
package lx.maven.model;

public class HelloWorld{
    public String sayHello() {
        return "Helo world!";
    }
}
```
和测试
```
package lx.maven.model;

import org.junit.*;
import org.junit.Assert.*;

public class HelloWorldTest{
    @Test
    public void testHello() {
        Assert.assertEquals("Hello world!", new HelloWorld().sayHello());
    }
}
```

这里需要注意的是分别新建包。

然后需要新建pom.xml，保存在项目根目录下。
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>lx.maven</groupId>
    <artifactId>maven01</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
        </dependency>
    </dependencies>

</project>
```

说明：
- `modelVersion`:固定，不管
- `groupId` 项目的包名
- `artifactId` 模块名
- `version` 版本，SNAPSHOT快照版本

编译：`mvn compile`
运行测试用例：`mvn test`

运行后我们看到项目更目录下创建了target文件夹，这个文件夹下面又有几个子文件夹，分别是：
- classes 生成的类文件字节码
- surefire-reports 测试报告

然后运行`mvn package`进行打包。打好的包在target根目录下。

## Maven核心知识

### 常用的构建命令

之前我们已经学习了以下命令：
- `mvn -v`:查看maven版本
- `mvn compile`: 编译
- `mvn test`: 测试
- `mvn package`:打包

本节主要学习以下两个命令：
- `mvn clean`: 删除target
- `mvn install`: 安装jar包到本地仓库中

我们新建一个项目`maven02`
```
package lx.maven02.util;

import lx.maven.model.HelloWorld;

public class Speak{
    public String sayHi() {
        return new HelloWorld().sayHello();
    }
}
```

```
package lx.maven02.util;

import org.junit.*;
import org.junit.Assert.*;

public class SpeakTest{
    @Test
    public void testsayHi() {
        Assert.assertEquals("Hello world!", new Speak().sayHi());
    }
}
```

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>lx.maven02</groupId>
    <artifactId>maven02-util</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.10</version>
        </dependency>
    </dependencies>

</project>
```

此时运行`mvn compile`会报错，表示找不到对应的`lx.maven.model`包。之所以会这样是因为我们需要讲`maven01`的项目运行`mvn install`先安装到本地仓库，然后在`maven02`项目中添加依赖：
```
<dependency>
    <groupId>lx.maven</groupId>
    <artifactId>maven01</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

### 自动建立目录骨架

之前一直手动创建很麻烦，maven提供了archetype插件，用于创建符合maven规定的目录骨架。

我们先创建一个项目目录`maven03`，然后在命令行输入：
```
mvn archetype:generate
```

然后会让我们选择`archetype`版本，选择最大的就好了，然后要求我们输入`groupId`，我们输入`lx.maven03`，`artifactId`我们输入`maven03-service`，`version`我们输入`1.0-SNAPSHOT`.然后会要求我们输入包名，不过会根据你之前输入的`groupId`自动提供一个，我们直接回车就可以了。最后会让我们确认信息，没问题就回车。

还有一种方法是一次性设置所有参数：
```
mvn archetype:generate -DgroupId=lx.maven03 -DartifactId=maven04-demo -Dversion=1.0SNAPSHOT -Dpackage=lx.maven03.demo
```

### maven中的坐标和仓库

坐标：构件的唯一标识。`groupId`,`artifactId`,`version`构成了坐标的基本要素。

仓库：管理项目依赖，分为两种：
- 本地仓库
- 远程仓库：如果本地仓库找不到，会到maven的全球仓库去找。

镜像仓库：打开maven/conf/settings.xml文件，在`mirrors`节添加：
```
<mirror>
     <id>maven.oschina.net</id>
     <mirrorOf>central</mirrorOf>
     <name>oschina maven mirror</name>
      <url>http://maven.oschina.net/content/groups/public</url>
   </mirror>
```
`mirrorOf`表示为哪个仓库指定镜像，默认是`central`，也可以使用`*`通配符。

现在看如何修改仓库位置：
默认情况下，仓库是在用户文件夹下的.m2文件中，我们可以修改settings.xml的`settings`下的位置：
```
<localRepository>D:/mavenrepo</localRepository>
```
然后将`settings.xml`文件复制到`d:/mavenrepo`下，这样以后要是修改maven版本就不用再次修改settings.xml.

### maven的声明周期和插件

完整的项目构建过程包括：
清理、编译、测试、打包、集成测试、验证、部署
clean\compile\test\package\install
maven声明周期：
- clean 清理项目
- default 构建项目
- site 生成项目站点

对于clean清理项目
- pre-clean 执行清理前的工作
- clean 清理上一次构建生成的所有文件
- post-clean 执行清理后的文件

default构建项目（最核心）
compile test package install都属于default阶段

site生成项目站点
- pre-site
- site
- post-site
- site-deploy

现在加入我们要添加插件source，让打包时同时打包源码。我们修改pom.xml，加入：
```
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-source-plugin</artifactId>
            <version>3.0.0</version>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>jar-no-fork</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```
说明：
- plugin的phase表明阶段
- goal表示目标

具体请看apache官方文档。

然后运行`mvn clean package`看到会生成源码的jar包。

### pom.xml常用元素介绍

pom.xml用于项目描述、组织管理、依赖管理和项目信息的管理。下面我们来介绍一下常用元素。
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!--指定了当前的POM版本-->
    <modelVersion>4.0.0</modelVersion>
    <!--主项目标识：反写公司网址+项目名-->
    <groupId></groupId>
    <!--项目名+模块名-->
    <artifactId></artifactId>
    <!--当前版本项目本：大版本号.分支版本号.小版本号;SNAPSHOT:快照，ALPHA:内部测试,BETA:公测,RELEASE:稳定版,GA:正式发布-->
    <version>0.0.1-SNAPSHOT</version>
    <!--打包方式，默认为jar .war,zip,pom-->
    <packaging>jar</packaging>

    <!--项目描述名，产生文档时使用-->
    <name>hi</name>
    <!--项目地址-->
    <url></url>
    <!--项目描述-->
    <description></description>
    <!--开发人员信息-->
    <developers></developers>
    <!--许可证信息-->
    <licenses></licenses>
    <!--组织信息-->
    <organization></organization>

    <!-- 依赖列表 -->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <type></type>
            <!-- 只在一个范围内使用 -->
            <scope>test</scope>
            <!-- 设置依赖是否可选，默认为false,默认是继承的 -->
            <optional></optional>
            <!-- 排除依赖传递列表。如果A依赖B，B依赖C，A就传递依赖C -->
            <exclusions>
                <exclusion>
                    <groupId></groupId>
                    <artifactId></artifactId>
                    <version></version>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <!-- 依赖的管理.不会实际被引入，主要用于定义在父模块，用于子模块继承 -->
    <dependencyManagement>
        <dependencies>
            <dependency></dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <!-- 插件列表 -->
        <plugins>
            <plugin>
                <groupId></groupId>
                <artifactId></artifactId>
                <version></version>
            </plugin>
        </plugins>
    </build>

    <!-- 在子模块中对父模块POM的继承 -->
    <parent></parent>
    
    <!-- 多模块 -->
    <modules></modules>
</project>
```

### 依赖范围

之前已经见过junit依赖：
```
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>3.8.1</version>
  <scope>test</scope>
</dependency>
```
我们平时在开发时，如果要使用某个框架就需要将该框架的jar包添加到项目的classpath中，maven为我们提供了3种classpath，分别是：
- 编译
- 测试
- 运行

所以`scope`就是用来指定依赖范围的。maven官方文档上说`scope`有六种取值：

- compile:编译，默认的范围，编译测试运行都有效
- provided:测试和编译时有效，运行时不会用
- runtime:测试和运行时有效
- test:测试，只在测试时有效
- system:测试和编译时有效,可移植性差，与本机想关联
- import：导入的范围，只使用在dependencyManagement中，表示从其他的pom中导入dependency的配置


### 依赖传递

A依赖B，B依赖C，则就可以说A间接依赖C。

首先，我们在B中引入对C的依赖(C应该`mvn compile install`).

```
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>3.8.1</version>
    <type></type>
    <!-- 只在一个范围内使用 -->
    <scope>test</scope>
    <!-- 设置依赖是否可选，默认为false,默认是继承的 -->
    <optional></optional>
    <!-- 排除依赖传递列表。如果A依赖B，B依赖C，A就传递依赖C -->
    <exclusions>
        <exclusion>
         <groupId></groupId>
         <artifactId></artifactId>
         <version></version>
        </exclusion>
    </exclusions>
</dependency>
```

有时我们需要指定JDK默认版本，在settings.xml中的`profile`配置版本。

### 依赖冲突

1. 短路优先
`A->B-C-X(jar)`和`A->D->X(jar)`，则A优先引用D所指向的X。
2. 先声明先优先
如果路径长度相同，则谁先声明，先解析谁。

### 聚合和继承

先来说聚合。

如果有多个项目，A\B\C，我们想要把他们聚合起来，一起安装，我们新建一个项目D，修改它的pom.xml:
```
<modules>
    <module>../A</module>
    <module>../B</module>
    <module>../C</module>
</modules>
```

然后运行`mvn clean install`就能直接安装A/B/C了。

再来说继承。
在父pom中添加：
```
<!-- 指定类型为POM -->
<packaging>pom</packaging>
<properties>
    <!-- 定义变量 -->
    <junit.version>3.8.1</junit.version>
</properties>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

在子POM中使用：
```
<parent>
    <groupId></groupId>
    <artifactId></artifactId>
    <version></version>
</parent>
```

## 使用maven构建web项目

我们使用maven创建一个web项目，然后发布到jetty中。

使用IDEA创建一个maven项目，选择`maven-archetype-webapp`类型，GroupId输入`lx.webdemo`，artifectId输入`webdemo`。
在http://mvnrepository.com/ 中添加
我们打开pom.xml，添加Java Servlet API依赖，注意，`servlet`的f范围是`provided`：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>lx.webdemo</groupId>
    <artifactId>webdemo</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>war</packaging>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <!--只在编译时和测试时运行-->
            <scope>provided</scope>
        </dependency>
    </dependencies>
</project>
```

然后添加`Jetty maven plugin`
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>lx.webdemo</groupId>
  <artifactId>webdemo</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>webdemo Maven Webapp</name>
  <url>http://maven.apache.org</url>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>3.1.0</version>
      </dependency>
  </dependencies>
  <build>
    <finalName>webdemo</finalName>
      <plugins>
          <plugin>
              <groupId>org.eclipse.jetty</groupId>
              <artifactId>jetty-server</artifactId>
              <version>9.4.0.M0</version>
              <executions>
                  <execution>
                      <phase>package</phase>
                      <goals>
                          <goal>run</goal>
                      </goals>
                  </execution>
              </executions>
          </plugin>
      </plugins>
  </build>
</project>
```

然后运行`mvn clean package`即可。