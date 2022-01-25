# JDK8-Stream使用

## 前言

JDK8，作为目前全球最多Java程序使用的JDK版本， Oracle 公司于 2014 年 3 月 18 日发布 以来，已经过去了快8年了，JDK8依旧是目前最稳定的Java 版本。

但是你确定会使用JDK8的新特性？

这里就来说说JDK8的Stream。

Stream是JDK8中的全新特性，Stream API（java.util.stream） 把真正的函数式编程风格引入到Java中。

Stream中文翻译过来叫做流，顾名思义就是以类似于输出方式来进行的函数。Stream目前主要应用于列表、集合中。通过对列表、集合获取对应的流来进行各种操作（比如过滤操作等）。

## Stream使用

### 创建Stream

我们可以通过4中方式来创建一个Stream：

1. 通过一个集合来创建 对应Stream：

   ```java
   # 存在一个List<User> userList
   Stream<User> stream = userList.stream();
   ```

2. 通过一个数组创建Stream：

   ```java
   int[] arr = new int[]{1,2,5,7};
   IntStream intStream = Arrays.stream(arr);
   ```

   这里的IntStream和Stream<Integer>是一样的。

3. 通过Stream.of方法：

   ```java
   Stream<String> stream = Stream.of("1","4","8");
   ```

4. 利用迭代器创建一个无线长度的流：

   ```java
   // 每隔5个数取一个,从0开始,限制5个数
   Stream.iterate(0,t->t+5).limit(5).forEach(System.out::println);
   // 取随机0-1浮点数
   Stream.generate(Math::random).limit(5).forEach(System.out::println);
   ```




其中Stream和Collection的区别就是：Collection只是负责存储数据，不对数据做其他处理，主要是和内存打交道。但是Stream主要是负责计算数据的，主要是和CPU打交道。

### 操作Stream

对于某个指定的Stream，我们可以对其进行操作从而达到对其原集合进行操作或者 输出对于新内容。

#### 筛选操作

```java
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 10, 20, 15, 19, 54, 64, 24, 12, 48, 34, 100);
// 筛选出大于50的数，打印出来
list.stream().filter(item->item>50).forEach(System.out::println);
```

其中`filter`是Stream操作的重要方法，它是一个过滤器，它接受一个判断函数（这里使用JDK8 Lambda匿名函数）。它将其函数作用于Stream中每个元素，输出为True则保留，输出为False则抛弃。



```java
list.stream().limit(5).forEach(System.out::println);
```

`limit`接受一个数值，表示只获取对应数值的数据。

```java
list.stream().distinct().forEach(System.out::println);
```

`distinct` 顾名思义，就是数据结果去重操作。



