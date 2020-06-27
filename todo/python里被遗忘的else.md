Title: python里被遗忘的else
Date: 2014-07-20 17:08
Category: Python
Tags: python
Author: 刘理想
Summary: python里被遗忘的else

本文讨论的是python中的`for … else`和`while…else`语法，最少被用到也最容易被误解的语法特性。

python中的`for`和`while`循环都又一个可选的`else`（有点像if语句和try语句中的那样），当循环正常完成时执行。也就是说，`else`只有在循环正常退出时才能执行。`break`、`return`或者循环中产生的异常都不会让`else`正常执行。

```python
>>> for i in range(5):
...     print(i)
... else:
...     print('Iterated over everything :)')
...
01234Iterated over everything :)
```

上面的代码里，我们遍历了`range(5)`。由于我们让循环正常完成，所以`else`语句也执行了，并且打印出了Iterated over everything:). 相反的，如果我们用`break`等语法退出循环，`else`就不会被执行。

```python
>>> for i in range(5):
...     if i == 2:
...         break
...     print(i)
... else:
...     print('Iterated over everything :)')
...01
```

注意，即使循环序列是空的，`else`也会被执行，毕竟所有的循环都已经正常走完。

```python
>>> for i in []:
...     print(i)
... else:
...     print('Still iterated over everything (i.e. nothing)')
...
Still iterated over everything (i.e. nothing)

而且，不要忘了哦，不要忘了哦，上面的内容对`while...else`也适用哦。

```python
>>> i = 0
>>> while i <= 5:
...     i += 1
...     print i
... else:
...     print 'Yep'
... 
12345Yep
```

但是，为什么呢？


`else`语句在循环中的一个通常用法是实现搜索循环，比如你要实现搜索符合某个条件的元素，如果没有符合条件的元素就执行额外的处理或者引发异常

```python
for x in data:
    if meets_condition(x):
        break
else:    
	# raise error or do additional processing 
```

如果没有`else`你就需要设置一个标识，然后检察这个标识用来确认是否有符合条件的元素。

```python
condition_is_met = False
for x in data:
    if meets_condition(x):
        condition_is_met = True
if not condition_is_met:
    # raise error or do additional processing
```

这并不是啥了不起的大事，毕竟你在其它语言中不得不这样做。但是如同python中的其它特性一样，else语句可以让这种做法更优雅，更Pythonic。在上面的利子中，适用else使你的代码更符合Python之道。

并不存在不得不在循环中适用`else`语句的情况，然和情况下你都可以适用标识变量等方法。但是`else`语句让代码更优雅，可读性更高。有些人认为它更Pythonic，表达的意思更清楚（Hi，我也是呢！），另一些或许认为它让人困惑并且多余。就我个人来说，我件事在循环中适用`else`语句，除非有更好的方法让代码变的更加易读(对我来说，可以归结为如何保持代码的可读性）