Title: Kotlin学习笔记
Date: 2017-08-28 22:03
Category: Java
Tags: Kotlin
Author: 刘理想

[TOC]

## 1. Kotlin基础

### 1.1 函数与变量

#### 1.1.1 函数

无返回值函数：

```kotlin
fun main(args: Array<String>) {
    println("Hello, world!")
}
```

有返回值函数：

```kotlin
fun max(x: Int, y: Int): Int {
    return if (x > y) x else y
}
```

**表达式函数体**

表达式和语句的区别：表达式有值，语句没有值。在kotlin中，除了循环(for, do和do/while)意外大多数控制语句都是表达式。

如果函数是由单个表达式构成的，可以用表达式作为函数的整体：

```kotlin
fun min(x: Int, y: Int): Int = if (x < y) x else y
```

对于表达式函数来说，可以省略返回类型。注意，也只有这种情况可以省略函数的返回类型。

```kotlin
fun min(x: Int, y: Int) = if (x < y) x else y
```

#### 1.1.2 变量

```kotlin
//1. 省略类型
val question = "How old are you?"
val answer = 42

//2. 声明类型
val question: String = "How old are you?"
val answer: Int = 42

//3. 如果没有初始化，就必须声明类型
val answer: Int
answer = 42
```

**可变变量与不可变量**

- `val`（来自value)-不可变引用。使用val声明的变量不能在初始化后再次赋值。对应于java的`final`变量
- `var`(来自variable)-可变引用。

尽可能使用`val`来声明变量，这样更加符合函数式的风格。

另外`val`引用本身虽然不可变，但是指向的对象有可能是可变的。

```kotlin
val languages = arrayListOf("Java");
languages.add("Kotlin")
```

#### 1.1.3 字符串模板

```kotlin
fun main(args: Array<String>) {
  val name = if (args.size > 0) args[0] else "Kotlin"
  println("Hello, $name")
}
```

如果要在字符串中使用`$`，使用转移字符`\$`

还可以使用更复杂的表达式:

```kotlin
fun main(args: Array<String>) {
    println("Hello, ${if (args.size >0) args[0] else "someone"}!")
}
```

### 1.2 类和属性

JavaBean类`Person`如下：

```java
public class Person {
    private final String name;
    public Ch0202Person(String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    } 
}
```

复制粘贴到kotlin中会自动转换为kotlin对应的类：

```kotlin
class Person(val name: String)
```

这种对象叫做**值对象**。注意，这里没有public，因为Kotlin中public是默认的可见性。

#### 1.2.1 属性



