Title: Gradle依赖管理
Date: 2016-05-10 22:40
Category: Java
Tags: Java, Gradle
Author: 刘理想

[TOC]

##1. 添加依赖

指定仓库是maven central

```
repositories {
    mavenCentral()
}
```

加入我们要使用apache commons中的csv包，首先在网页https://commons.apache.org/proper/commons-csv/ 中找到对应的maven：

```
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.3</version>
</dependency>
```

上面的是xml格式，我们把它转换成gradle格式：

```
dependencies {
    compile group: 'org.apache.commons', name: 'commons-csv', version: '1.3'
    //等价于
    //compile 'org.apache.commons:commons-csv:1.3'
}
```

然后我们在gradle面板刷新项目。

也可以在Gradle面板右键选择`auto import`.

也可以在命令行运行`./gradlew`用来在电脑上安装gradle。以及`./gradlew dependencies`是用来安装依赖的。

##2. 使用依赖

新建类`io.github.liulixiang1988.Main`类

```
package io.github.liulixiang1988;

import org.apache.commons.csv.CSVFormat;
import org.apache.commons.csv.CSVPrinter;

import java.io.IOException;

public class Main {
    public static void main(String[] args) {
        try {
            CSVPrinter printer = new CSVPrinter(System.out, CSVFormat.EXCEL);
            printer.printRecord("Liu", "Lixiang", 1988);
            printer.printRecord("L", "L", 1988);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

对于不知道具体名字的包，比如HTML解析包，我们可以到http://search.maven.org/ 去搜索`html parser`，但有时候搜索的结果不尽人意，此时我们可以使用google搜索`maven html parser`，会返回http://mvnrepository.com/ 网站对`maven`的搜索。

我们也可以使用github搜索，条件是`language:java`，然后按星排名。