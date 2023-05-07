---
title: 不要再这样用System统计时长了，太Out了...
tags: JK
excerpt: StopWatch
---
> 哈喽，大家好，我是程小南，导航搬砖工一名，致力于为大家淘出更多有趣好使的生产力工具！

![](https://navtool.gitee.io/blog/assets/imgs/20221029/fm.png)

小南最近有点郁闷。

用了一辈子的System.currentTimeMillis，突然不行了。

事情是这样的，测试我噻在测试的时候反馈一个接口，特别慢。

小南看了下感觉都挺正常，也不知道哪里有问题。

于是乎一顿输出，

打印了满屏的log。

好死不死，老大看到满屏的System.currentTimeMillis。

又是一顿输出，

扔了一个单词 ”StopWatch“、

### System.currentTimeMillis()

在做有些业务的时候，需要记录该业务的执行时间，这时候想到`Date`来记录处理，也就是常用的System.currentTimeMillis()方法，在代码块执行前后分别记录，输出差值。

```java
Long startTime = System.currentTimeMillis();
//业务代码
Long endTime = System.currentTimeMillis();
System.out.println("本次耗时：" + (endTime - startTime));
```

当当看上面的代码是不是简单明了，当某些情况下，比如小南这次满屏的System.currentTimeMillis(),闭上眼可以想象满屏的神兽在心中跑过。如果想做进一步控制，一想什么也解决不了。

这时会想是否有框架会一些法，刚好可以满足这场景？

### StopWatch初始

StopWatch貌似`Spring`、`Apache`两个顶级框架都有。小南用的是`Spring `框架中的。

StopWatch 是位于 org.springframework.util 包下的一个工具类，通过它可方便的对程序部分代码进行计时(ms级别)，适用于同步单线程代码块。

### StopWatch使用

想要使用它，需要引入对应的包，如Maven 中引入 Spring 核心包，正常情况下我们的项目自动引入了该包

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>${spring.version}</version>
</dependency>
```

StopWatch常见的方法有。

![](https://navtool.gitee.io/blog/assets/imgs/20221029/1.png)

上例子

 ```java
//建议使用有参可以对任务分类
StopWatch stopWatch = new StopWatch("统计模块任务执行情况");
// 任务一(1秒)
stopWatch.start("task1");
Thread.sleep(1000 * 1);
System.out.println("当前任务名称：" + stopWatch.currentTaskName());
stopWatch.stop();
// 任务二(2秒)
stopWatch.start("task2");
Thread.sleep(1000 * 2);
stopWatch.stop();
// stop后
System.out.println("stop-currentTaskName："+stopWatch.currentTaskName());
// 最后一个任务的相关信息
System.out.println("最后一个任务：" + stopWatch.getLastTaskName());
System.out.println("最后一个任务：" + stopWatch.getLastTaskTimeMillis());
System.out.println("最后一个任务：" + JSON.toJSON(stopWatch.getLastTaskInfo()));
//总任务详情
System.out.println("总耗时：" + stopWatch.getTotalTimeMillis());
System.out.println("总任务数：" + stopWatch.getTaskCount());
System.out.println("所有任务详情：" + JSON.toJSON(stopWatch.getTaskInfo()));
//耗时情况
System.out.println("prettyPrint："+stopWatch.prettyPrint());
System.out.println("shortSummary："+stopWatch.shortSummary());

 ```

console输出情况

```java
当前任务名称：task1
stop-currentTaskName：null
最后一个任务：task2
最后一个任务：2013
最后一个任务：{"taskName":"task2","timeMillis":2013,"timeSeconds":2.013}
总耗时：3024
总任务数：2
所有任务详情：[{"taskName":"task1","timeMillis":1011,"timeSeconds":1.011},{"taskName":"task2","timeMillis":2013,"timeSeconds":2.013}]
prettyPrint：StopWatch '统计模块任务执行情况': running time (millis) = 3024
-----------------------------------------
ms     %     Task name
-----------------------------------------
01011  033%  task1
02013  067%  task2

shortSummary：StopWatch '统计模块任务执行情况': running time (millis) = 3024
```

### 总结

StopWatch没有使用同步，非线程安全的。

同一个StopWatch对象，只能运行一个task，再次开启(start)task任务，必须要停掉(stop)该对象正常使用的task，切记。

若要一次开启多个，需要new不同的StopWatch实例

简单总结一句，Spring提供的计时器StopWatch对于秒、毫秒为单位方便计时的程序，尤其是单线程、顺序执行程序的时间特性的统计输出支持比较好。能够展示几个任务分别执行的时间，占比状况等。

### 好消息

时隔2个月，摸鱼群再次限时开放了。

![](https://navtool.gitee.io/blog/assets/imgs/20221027/111.jpg)

程序员指南读者交流群（摸鱼、白嫖技术课程为主），又不定时开放了，感兴趣的朋友，下方公号内回复：`666`。

![](https://navtool.gitee.io/blog/assets/imgs/erweima.jpg)

>  我是程小南,感谢各位童鞋的：点赞、收藏和在看，我们下期更精彩！














