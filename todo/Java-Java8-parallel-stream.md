Title: Java8中的并行流
Date: 2016-06-22 20:00
Category: Java
Tags: Java
Author: 刘理想

*此笔记是我在阅读《Java8实战》中的一些记录。*

Java8中增加了流(stream)的概念，为数据的处理带来了很大的方便。

并行流将数据分割成不同的块，并且用不同的线程处理不同的块。

现在要根据输入`n`，求从1到n的和，这里我们不使用`n*(1+n)/2`的方式，而是使用累加。我们分别写出迭代、顺序流、并行流的写法。

```java
public class ParallelStreams {
    public static long sequentialSum(long n) {
        return Stream.iterate(1L, i->i+1)
                .limit(n)
                .reduce(0L, Long::sum);
    }

    public static long iterativeSum(long n) {
        long result = 0;
        for (long i = 1L; i <= n; i++) {
            result += i;
        }
        return result;
    }

    public static long parallelSum(long n) {
        return Stream.iterate(1L, i->i+1)
                .limit(n)
                .parallel()
                .reduce(0L, Long::sum);
    }
}
```

我们可以看到，将顺序流转换为并行流只需要`parallel()`方法即可。

现在我们来测量这几个方法的性能：
```java
public static long measureSumPerf(Function<Long, Long> adder, long n) {
    long fastest = Long.MAX_VALUE;
    for (int i = 0; i < 10; i++) {
        long start = System.nanoTime();
        long sum = adder.apply(n);
        long duration = (System.nanoTime() - start) / 1_000_000; //毫秒
        System.out.println("Result: "+sum);
        if(duration < fastest) fastest = duration;
    }
    return fastest;
}
```

然后分别测试这三个方法:

```java
public static void main(String[] args) {
    System.out.println("顺序流:" + measureSumPerf(ParallelStreams::sequentialSum, 10_000_000)+" 毫秒");
    System.out.println("循环:" + measureSumPerf(ParallelStreams::iterativeSum, 10_000_000)+" 毫秒");
    System.out.println("并行流:" + measureSumPerf(ParallelStreams::parallelSum, 10_000_000)+" 毫秒");
}
```
结果出乎我们的预料：
```
顺序流:173 毫秒
循环:4 毫秒
并行流:697 毫秒
```

为什么会这样？
- `iterate`生成的是`Stream<Long>`对象，需要拆箱才能求和；
- `iterate`很难分割成独立的小块，因为每次应用这个函数都需要前一次应用的结果，也就是说它其实是顺序执行的。这样反而在并行时增加了分配线程的开销。

这里我们使用`LongStream.rangeClosed`方法来优化，它的特点有：
- `LongStream.rangeClosed`直接产生原始类型的`long`数字，没有拆箱与装箱的开销。
- `LongStream.rangeClosed`产生一个数字范围，很容易拆分成多个小块。

我们再用`LongStream.rangeClosed`写出顺序和并行计算的版本：

```java
public static long parallelRangedSum(long n) {
    return LongStream.rangeClosed(1, n)
            .parallel()
            .reduce(0, Long::sum);
}

public static long measureSumPerf(Function<Long, Long> adder, long n) {
    long fastest = Long.MAX_VALUE;
    for (int i = 0; i < 10; i++) {
        long start = System.nanoTime();
        long sum = adder.apply(n);
        long duration = (System.nanoTime() - start) / 1_000_000; //秒
        System.out.println("Result: "+sum);
        if(duration < fastest) fastest = duration;
    }
    return fastest;
}
```

然后进行测试：
```java
System.out.println("顺序Range流:" + measureSumPerf(ParallelStreams::rangedSum, 10_000_000)+" 毫秒");
System.out.println("并行Range流:" + measureSumPerf(ParallelStreams::parallelRangedSum, 10_000_000)+" 毫秒");
```

执行结果如下：
```
顺序Range流:7 毫秒
并行Range流:3 毫秒
```
这个速度就快多了，但是我们要记住，使用并行时，在多个内核直接移动数据的代价也比较大，因此，要保证在内核中并行执行工作的时间比在内核之间传输数据的时间要长才比较划算。

现在我们来总结一下如何高效使用并行流：
- 如果用循环还是顺序流或者是并行流，像我们上面那样测试一下；
- 注意装箱，尽量使用`IntStream`, `LongStream`，和`DoubleStream`来避免装箱拆箱;
- 有些操作在并行流上性能很差，比如`limit`,`findFirst`等依赖顺序的操作。`unordered`方法可以把有序流转为无序流，使用`findAny`等好很多，在无序流上用`limit`也好很多;
- 计算流水线操作总成本，处理单个元素用时越多，并行就越划算；
- 对于较小的数据量，用并行不一定是好事儿；
- 数据结果是否易于分解，比如`ArrayList`比`LinkedList`易于分解，`range`创建的原始流也易于分解；
- 终端操作中的合并大家是否很大，大了也不划算。

