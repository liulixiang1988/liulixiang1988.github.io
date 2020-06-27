Title: Kotlin学习笔记
Date: 2016-03-01 22:30
Category: Java
Tags: Kotlin,学习笔记
Author: 刘理想
Summary: Kotlin语法介绍

[TOC]

##0. Kotlin简介

###0.1 简介

###0.2 Kotlin安装

###0.3 Hello, Kotlin

```kotlin
fun main(args:Array<String>)
{
    println("Hello, Kotlin!")
}
```

##1. 类型和变量

###1.1 变量声明(Variable Declarations)

```kotlin
fun main(args: Array<String>) {
    val_declarations()
}

fun val_declarations(){
    //val声明的变量不能更改
    val a:Int = 64
    val b:Long = 123

    //这种事不行的,必须每个占一行:
    //val c = 3, d = 4

    val c:Float = 3.4f //后面跟'F'或者'f'
    val d:Double = 3.4
    val e:Double = 12.3e5
    //不赋值的val声明
    val f: Int
    f = 13

    val g:StringBuffer = StringBuffer("test")
    g.replace(0, 1, "T")
    //打印
    println("$a $b $c $d $e $f")
    println("$g")

    //可更改的变量
    var h:Int = 123
    h = 45
    println("$h")
}
```

###1.2 类型推导(Type Deduction)
类型能够根据赋值进行推到

```kotlin
fun main(args: Array<String>) {
    val_declarations()
}

fun val_declarations(){
    //val声明的变量不能更改
    val a = 64
    val b= 123
    val c = 3.4f //后面跟'F'或者'f'
    val d = 3.4
    val e = 12.3e5
    //不赋值的val声明，变量类型不能省略
    val f: Int
    f = 13

    val g = StringBuffer("test")
    g.replace(0, 1, "T")
    //打印
    println("$a $b $c $d $e $f")
    println("$g")

    //可更改的变量
    var h = 123
    h = 45
    println("$h")
}
```

###1.3 Ranges

```kotlin
/**
 * Created by liulixiang on 16/2/26.
 */
fun main(args: Array<String>) {
    ranges()
}

fun ranges(){
    val a: IntRange = 1..10 //闭区间,而不是左闭右开. 1, 2, ..., 10(含10)
    //遍历range,使用in语法
    for (x in a) println(x)

    //除了上面的声明方式,还有以下声明方式:
    val b = 1.rangeTo(10)

    println("=====逆序=====")
    val c:IntProgression = b.reversed()
    for(x in c) println(x)

    println("=====range求和=====")
    println("sum of ints $c = ${c.sum()}")

    println("=====递减的range=====")
    val d = 10.downTo(1) //不能用10..1
    for(x in d) println(x)

    println("=====省略括号======")
    val e = 10 downTo 1
    for(x in e) println(x)

    println("=====根据变量声明range======")
    val m = 8
    val n = -3
    val f = IntRange(Math.min(m, n), Math.max(m, n))
    for(x in f) println(x)

    println("=====指定步长======")
    val g = 100 downTo 1 step 3
    for(x in g) print("$x\t")
    println()
}
```

###1.4 数组


###1.5 Strings and String Interpolation

```kotlin
fun main(args: Array<String>) {
    characters_and_strings()
}

fun characters_and_strings() {
    //字符
    println("=====字符=====")
    val a:Char = '\u0041'
    if (a.toInt() == 65){
        println("match")
    }

    //字符串
    println("=====字符=====")
    val b:String = "Hello"
    println(b)
    println("取字符串中的字符:${b[0]}")
    println("遍历字符串中的字符:")
    for(letter in b) print("$letter\t")
    println()

    //原始字符串
    println("=====原始字符串=====")
    val raw = """Hello
"test"
world"""
    println(raw)

    //字符串模板
    println("=====字符串模板=====")
    val c = 123.0
    val d = "c = $c, price = ${'$'}${c/10}"
    println(d)

    //range
    println("=====range=====")
    val e = "${(10 downTo 1).toList().map { it.toString().toList() }.joinToString()}"
    println(e)
}
```

##2. 控制流

###2.1 Nullability
在kotlin中`var x:String = null`是不允许的，因为它要求`x`不能为空。
```
fun main(args: Array<String>){}
```
对于上面的`args`表示的是由不能为空的`String`组成的不能为空的`Array`。

如果为null该怎么办，在类型名后添加`?`。

```kotlin
fun main(args: Array<String>) {
    nullability()
}

fun nullability(){
    var y:String? = null
    //下面的是错误的
    //println(y.length)
    var len: Int? = y?.length
    println(len)
    //或者这样返回-1，其中?:类似else，也就是说如果y是Null，就返回-1
    var len2:Int = y?.length ?: -1
    println(len2)

    //注意：1.系统库其实自动添加了null 检查，比如 System.out.println，其实是System?.out?.println
    //关闭null检查：y!!.length 如果此时y为null会抛出异常
}
```
###2.2 If 语句

第一种是 `? :`形式
if语句直接返回值。

```kotlin
fun main(args: Array<String>) {
    if_statement()
}

fun if_statement() {
    //? :
    val temp = 20
    val feel = if (temp < 10)
        "cold"
    else if (temp > 20)
        "warm"
    else "ok"

    println("it feels $feel outside")
    println("it is ${if(temp > 20) "warm" else "ok"} today")
}
```

###2.3 根据循环自动类型转换(Flow-Based Typing)

```kotlin
fun flow_based_typing() {
    var s:String? = "hjkh"
    if(s!=null){
        //不需要s?.length
        println(s.length)
    }
    var x: Cloneable = intArrayOf(1, 2, 3)
    if (x is IntArray){
        //不需要(x as IntArray).size
        println(x.size)
    }
}
```

###2.4 for遍历(for Iteration)

```kotlin
fun for_loop(){
    //普通for循环
    for (a in (10 downTo 1)) print("$a\t")
    println()

    //数组for循环
    val ints = intArrayOf(4, 3, 2, 1)
    for((index, value) in ints.withIndex()){
        println("$index:\t$value")
    }

    //字典for循环
    val capitals = mapOf("Paris" to "France", "London" to "UK")
    for ((capital, country) in capitals){
        println("${country}'s capital is $capital")
    }
}
```

###2.5 when表达式

##3. 函数

###3.1 顶层函数(Top-Level Function)
谈论顶层函数之前需要先讨论package，如果没有指定package，就会放到默认包中。

```kotlin
package KotlinSamples.Functions
fun say_hello(){
    println("Hello")
}
```

```kotlin
import KotlinSamples.Functions.*
fun main(args: Array<String>) {
    say_hello()
}
```
###3.2 返回类型和参数(Return Types and Arguments)

```kotlin
fun main(args: Array<String>) {
    println("4*3 is ${triple(4)}")
    println("4*4 is ${sqr(4)}")
    println("pay ${calculate_wages(160)}")
    //命名参数
    println("pay ${calculate_wages(hourly_rate = 20.0)}")
}

fun triple(x:Int): Int{
    return x*3
}

fun sqr(x:Int) = x * x

//默认参数
fun calculate_wages(hours:Int = 160, hourly_rate:Double = 100.0): Double{
    return hours*hourly_rate
}
```

###3.3 可变长参数函数(Variable-Argument Functions)

```kotlin
fun main(args: Array<String>) {
    println("result is ${sum_up(1, 3, 5)}")
    println("result is ${sum_up2(4, 5, 6)}")
    //解包操作
    val stuff = intArrayOf(1,2 ,3)
    println(sum_up(3, 5, *stuff, 1, 9))
}

//可变长参数
fun sum_up(vararg values:Int):Int{
    var result = 0
    for (v in values){
        result += v
    }
    return result
}

//函数式的解法
fun sum_up2(vararg values:Int):Int{
    return values.sum()
}
```
###3.4 内签函数(Local a.k.a. Nested or Inner Functions)

```kotlin
fun main(args: Array<String>) {
    //解包赋值
    val (x1, x2) = solve_quadratic_equation(1.0, 10.0, 16.0)
    println("x1 = $x1, x2 = $x2")
}

fun solve_quadratic_equation(a:Double, b:Double, c:Double): Pair<Double, Double>
{
    fun calculate_discriminant(): Double{
        return b*b - 4*a*c
    }
    val root_disc = Math.sqrt(calculate_discriminant())
    return Pair((-b+root_disc)/(2*a), (-b-root_disc)/(2*a))
}
```

###3.5 扩展函数和扩展属性(Extension Functions and Properties)

```kotlin
import java.util.*

fun main(args: Array<String>) {
    extension_functions()
}
//扩展函数
fun <T> ArrayList<T>.swap(index1:Int, index2:Int){
    //this指向实例
    val temp = this[index1]
    this[index1] = this[index2]
    this[index2] = temp
}

//给所有的对象添加函数，用Any?
fun Any?.print(){
    if( this == null) println("Object is null")
    else println("Object is ${this.toString()}")
}

//扩展函数
val String?.empty: Boolean
    get() = (this == null || this.length == 0)

fun extension_functions(){
    val mylist = arrayListOf(1, 2, 3)
    //调用扩展函数
    mylist.swap(0, 2)
    println(mylist)
    mylist.print()

    val s:String? = null
    s.print()
    //调用扩展属性
    println(s.empty)
    println("".empty)
}
```

###3.6 中缀函数(Infix Functions)

```kotlin
fun main(args: Array<String>) {
    infix_functions()
}
//定义中缀函数
infix fun Double.averageWith(other: Double):Double{
    //this是左边的运算符
    return (this + other) / 2.0
}
fun infix_functions(){
    val x = 'z' downTo 'a'
    for(a in x) print("$a\t")
    println()

    val capitals = mapOf("Paris" to "France", "London" to "UK")
    for((key, value) in capitals)
        println("$key\t$value")
    println()

    //调用中缀函数
    val a = 3.0
    val b = 10.0
    println("The average of $a and $b is ${a averageWith b}")
}
```

##4 Lambda函数

###4.1 Lambda函数

```kotlin
package KotlinSamples.Lambda

fun main(args: Array<String>) {
    val product = {x:Int, y:Int -> x * y}
    println("${product(2, 3)}")
    //可以把签名放在前面
    val product2:(Int, Int)->Int = {x, y -> x* y}
    println("${product2(4, 6)}")

    //lambda作为参数
    val numbers = listOf(1, 2, 3, 4)
    val n = numbers.count { it > 2 } //等价于number.count({x->x>2})
    println(n)

    //闭包,kotlin中允许在lambda中修改外界的值
    var sum = 0
    numbers.forEach { sum += it }
    println("sum of $numbers is $sum")
}
```
###4.2 高阶函数
高阶函数是含有函数作为参数的函数，或者是返回值是函数的函数。
```kotlin
fun main(args: Array<String>) {
    val numbers = listOf(7, 1, 3, 4)
    val maxvalue = max(numbers) {x, y -> x < y}
    println(maxvalue)
}

fun <T> max(collection: Collection<T>, less:(T, T)->Boolean): T?{
    var max:T? = null
    for(x in collection){
        if (max == null || less(max, x))
            max = x
    }
    return max
}
```

###4.3 带接受者的函数类型

```kotlin
fun main(args: Array<String>) {
    //Function type with receiver
    //类似扩展函数,但是只能作用于局部,而且这个函数是个lambda表达式,被存储在变量中
    fun_type_with_recevier()
    //由于increaseBy是局部变量,因此下面的代码无法执行
    //var y = 2
    //y.increaseBy(10)
}

fun fun_type_with_recevier(){
    //声明形式类似扩展函数,但是返回到变量中
    val increaseBy = fun Int.(value:Int) = this + value
    var x = 1
    println("$x + 3 = ${x.increaseBy(3)}")
}
```
###4.4 Functors

不知道为什么，一直报错：Error:(10, 15) Kotlin: operator modifier is required on 'invoke' in 'Average'

```kotlin
class Average{
    public fun invoke(vararg values: Double):Double{
        val sum = values.sum()
        return sum/values.size
    }
}
fun main(args: Array<String>) {
    val a = Average()
    //functor
    val avg = a(1.0, 2.0, 3.0)
    println(avg)
//    fun String.invoke():String{
//        return "("+this+")"
//    }
//    val str = "hello"
//    println("${str()}")
}
```

##5 类

###5.1 属性(property)

```kotlin
class Person{
    lateinit var name:String //如果不初始化,需要加lateinit
    var age:Int = -1
    val canVote:Boolean
        get() = age > 16
    var ssn = "00000"
        get() = field
        set(value) {
            println("$name's ssn is changed to $value")
            field = value
        }
}

fun main(args: Array<String>) {
    var me = Person()
    me.name = "lixiang"
    me.age = 28
    println("${me.name} can ${if (!me.canVote) "not " else ""}vote")
    me.ssn = "123456"
}
```

###5.2 构造函数和初始化

```kotlin
//注意,class的构造中需要给参数带上var或者val才是添加属性
class Human(var name:String, val age:Int){
    //初始化
    init{
        println("$name is $age")
    }
}
fun main(args: Array<String>) {
    var me = Human("Lixiang", 28)
    println("My name is ${me.name}, and my age is ${me.age}")
}
```

###5.3 委托属性(Delegated Properties)

```kotlin
package KotlinSample.demo3

import kotlin.properties.Delegates
import kotlin.reflect.KProperty

class Delegate{
    operator fun getValue(thisRef:Any?, property: KProperty<*>):String{
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value:String){
        println("$value has been assigned to '${property.name}' in $thisRef")
    }
}

class Person{
    //委托
    var name: String by Delegate()
    //委托的一种用法,惰性
    val lazyValue: String by lazy {
        println("lazyValue初始化")
        "test"
    }

    //委托的用法,通知,其中"John"是初始值
    var othername: String by Delegates.observable("John") {
        prop, old, new -> println("${prop.name} $old -> $new")
    }
}
fun main(args: Array<String>) {
    var p = Person()
    p.name = "Lixiang"
    val s = p.name
    println(s)
    //只求一次值
    println(p.lazyValue)
    println(p.lazyValue)
    println(p.lazyValue)
    p.othername = "Jack"
}
```

###5.4 数据类(Data Classes)

数据类主要用来存储数据

```kotlin
package KotlinSample.demo4

//data class用来保存数据,他有以下几种方法
//equals()/hashCode()
//toString()
//copy() 深拷贝
//destructuring 解包操作
data class User(val name:String, val age: Int){
    //你也可以重写
    override fun toString(): String{
        return "My name is $name, age is $age"
    }
}

fun main(args: Array<String>) {
    val a = User("Lixiang", 20)
    println(a)
    val b = User("Lixiang", 20)
    println(a == b)
    println(a === b)
    val c = a.copy()
    println(c == a)
    println(c ===a)
    //解包
    val (name, age) = c
    println("$name, $age")
}
```
###5.5 单例(Singleton)

```kotlin
package KotlinSample.demo5

class PointFactory{
    fun NewCartersianPoint(x:Double, y:Double): Point{
        return Point(x, y)
    }

    fun NewPolarPoint(rho: Double, theta: Double): Point{
        return Point(rho*Math.cos(theta), rho*Math.sin(theta))
    }
}

class Point(val x: Double, val y: Double)

fun main(args: Array<String>) {
    var pf = PointFactory()
    var point = pf.NewPolarPoint(3.0, Math.PI/2)
    print("${point.x} ${point.y}")
}
```
###5.6 伴随对象(Companion Objects)

Kotlin中没有静态方法，使用伴随对象来实现类似静态方法的效果

```kotlin
package KotlinSample.demo6

interface IFactory<T> {
    fun create(): T
}

//注意,伴随对象并不是真正的static的
class Point(var x: Double, var y: Double){
    //伴随对象,使用companion object修饰,其中Factory可以注释掉,也就是下面的代码也是可用的:
    //companion object {
    //伴随对象还能实现接口
    companion object /*Factory*/ : IFactory<Point>{
        override fun create(): Point {
            return Point(0.0, 0.0)
        }

        fun createCarterian(x: Double, y: Double): Point = Point(x, y)
        fun createPolar(x: Double, y: Double): Point = Point(x, y)
    }
}

fun main(args: Array<String>) {
    var simplePoint = Point(1.0, 2.0)

    //var factoryPoint = Point.Factory.createCarterian(1.0, 2.0)
    //类似静态方法的调用
    var factoryPoint2 = Point.createPolar(1.0, 2.0)

    var defaultPoint = Point.create()
    //或者如果没有给伴随对象起名字,也可以用下面这个方法
    var defaultPoint2 = Point.Companion.create()
}
```

###5.7 继承(Inheritance)

```kotlin
//只有open的class才能被继承
open class Person(val name: String){
    //同样只有open方法才能被overwrite
    open fun talk(){
        println("I'm $name")
    }
}
//父类已经有了name,所以不能再给这里的name添加val/var
//Person有构造方法,因此必须对其进行调用
class Manager(name: String, var subordinates: List<Person>) : Person(name){
    //重写方法
    override fun talk(){
        println("I'm $name, I have ${subordinates.size} subordinates")
    }
}

fun main(args: Array<String>) {
    val lixiang = Person("理想")
    lixiang.talk()

    val boss = Manager("龙", listOf(lixiang))
    boss.talk()
}
```

###5.8 接口(Interfaces)

###5.9 类委托(Class Delegation)


---
##6 其他

###6.1 枚举(Enumerations)

###6.2 异常(Exceptions)

###6.3 操作符重载(Operator Overloads)

###6.4 Type-Safe Builders