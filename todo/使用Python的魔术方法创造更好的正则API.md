Title: [翻译]使用Python的魔术方法创造更好的正则API
Date: 2014-07-27 10:28
Category: Python
Tags: python
Author: 刘理想

原文[在此](http://charlesleifer.com/blog/playing-with-python-magic-methods-to-make-a-nicer-regex-api/)

我的一个同事说他怀念Ruby的正则表达式语法糖。我没有用过Ruby的正则表达式，但是我熟悉Python的，并且知道它的API需要一些语法糖来弥补一下。

首先，从正则表达式中获取分组需要2步。第一步，调用match()或者search()，并且将结果付值给一个变量。然后，需要检察结果是否为None(表示是否有找到匹配)。最后，如果匹配确实存在，你可以安全地取出分组。下面是一个例子：

    >>> import re
    >>> match_obj = re.match('([0-9]+)', '123foo')
    >>> match_obj  # What is `match_obj`?
    <_sre.SRE_Match object at 0x7fd1bb000828>
    >>> match_obj.groups()('123',)
    >>> match_obj = re.match('([0-9]+)', 'abc')
    >>> match_objNone

依我来看，如果能像下面这样就更好了：

    >>> re.get_matches('([0-9]+)', '123foo')('123',)
    >>> re.get_matches('([0-9]+)', 'abc')None

另一个我常遇到的问题是弄混re.sub的参数，re.sub是用来查找和替换用的。所需要的参数按照顺序依次是模式(pattern)，替换(replacement)，还有字符串。由于更重各样的原因，对我来说search_string放在replacement前面看着更起来更直接。

不幸的是，弄混这些参数将会导致看上去正确的结果。下面是个例子，为了把单词foo用bar来代替。

    >>> re.sub('foo', 'replace foo with bar', 'bar')
    'bar'
    >>> re.sub('foo', 'bar', 'replace foo with bar')
    'replace bar with bar'

在上面例子中，有时我们会推测输入的字符串仅仅是foo

##糖

为了有趣，我把加了一些语法糖的helper class和python的正则表达式放在了一起。我不是要每个人都这样做，我的目的是为了让它更有趣，能给你在改进其它库的语法时给你一些启发。

在我展示具体实现之前，先说说我设计的几个API的例子。

在一步操作中同事完成查找和匹配。

    >>> def has_lower(s):
    ...     return bool(R/'[a-z]+'/s)

    >>> has_lower('This contains lower-case')
    True
    >>> has_lower('NO LOWER-CASE HERE!')
    False

获取分组也很简单

    >>> list(R/'([0-9]+)'/'extract 12 the 456 numbers')
    ['12', '456']

最后，你可以适用字符串插入操作来执行替换。

    >>> R/'(foo|bar)'/'replace foo and bar' % 'Huey!'
    'replace Huey! and Huey!'

则么样，是不是很有意思？

##实现

具体的实现时非常直接的，并且依赖于Python的魔术方法来提供API。如果有一个简单的技巧，那就是使用元类(metaclass)来实现classmethod的操作符重载。

    import re

    class _R(type):
        def __div__(self, regex):
            return R(regex)

    class R(object):
        __metaclass__ = _R

        def __init__(self, regex):
            self._regex = re.compile(regex)

        def __div__(self, s):
            return RegexOperation(self._regex, s)

    class RegexOperation(object):
        def __init__(self, regex, search):
            self._regex = regex
            self._search = search

        def search(self):
            match =  self._regex.search(self._search)
            if match is not None:
                return match.groups()

        def __len__(self):
            return self._regex.search(self._search) is not None

        def __mod__(self, replacement):
            return self._regex.sub(replacement, self._search)

        def __iter__(self):
            return iter(self._regex.findall(self._search))

一步一步的分解操作，希望它能简化背后发生了什么。

调用R/<something>将会调用_R类的__div__方法，它使一个工厂方法，用来生产R实例。

    >>> R/'foo'
    <rx.R at 0x7f77c00831d0>

然后，通过调用R对象的__div__，我们获得一个RegexOperation实例，所以R.__div__是另一种工厂方法。

    >>> r_obj = R/'foo'
    >>> r_obj / 'bar'
    <rx.RegexOperation at 0x7f77c00837d0>

最后一个对象，RegexOperation，实现了一些魔术方法来匹配、替换和测试匹配的存在性。

##评论

###1楼
很酷的运算符重载＋1 造轮子-1

你的代码：
    >>> list(R/'([0-9]+)'/'extract 12 the 456 numbers')
    ['12', '456']

我认为比Python本身的要复杂：

    >>> re.findall('([0-9]+)', 'extract 12 the 456 numbers')
    ['12', '456']

如果没有明显的分组，re.findall()将会返回一个group 0的匹配的list；如果定义了多个分组，re.findall()返回由每个分组组成的tuple构成的list：

    >>> re.findall('([A-Z])([0-9]+)', 'A133 B873 xxx yyy C946')
    [('A', '133'), ('B', '873'), ('C', '946')]

非常有趣的文章。我赞同你对re.sub参数列表顺序的看法。

我想说re.findall()返回空列表，如果没有匹配就返回空列表。

同样，re.finditer()提供了一个匹配对象的迭代器。

###2楼
正则表达式高级教程：
http://www.cofoh.com/advanced-regex-tutorial-python/traps

###3楼
使用parsely库可以用来写复杂的正则。
