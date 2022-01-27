# JDK8-Lambda表达式和函数接口

## 前言

JDK8中一个重要的新特性是 Lambda表达式的实现，众所周知Lambda表达式在函数编程语言JavaScript、Python等都存在。

Java *Lambda表达式*的一个重要用法是简化某些*匿名内部类*（`Anonymous Classes`）的写法，甚至绝大一部分人可能会把Lambda表达式与匿名函数分不清楚。

但实际上Lambda表达式并不仅仅是匿名内部类的语法糖，JVM内部是通过*invokedynamic*指令来实现Lambda表达式的。它是一种新的函数类表达方式。

## Lambda表达式做匿名内部类使用

Lambda表达式可以用来当做匿名内部类来表达：

比如需要新建一个线程时，在JDK8之前一种常见的匿名内部类写法是这样：

```java
new Thread(new Runnable(){ // 接口名
	@Override
	public void run(){ // 方法名
		System.out.println("Thread run()");
	}
}).start();
```

上述代码给`Tread`类传递了一个匿名的`Runnable`匿名内部类对象，重载`Runnable`接口的`run()`方法来实现相应逻辑。

但仔细看看，这种匿名内部类除了没有定义生成的类的名字之外，其实根本简化多少！所以在Lambda表达式出来前匿名内部类的定位就十分尴尬:face_with_head_bandage:，用吧，结果工作量几乎一样，不用吧，又觉得可惜，属于那种上又上不去下又下不了那种，卡在那儿了。

而在JDK8 Lambda表达式中，则变得十分简洁：

```java
new Thread(
		() -> System.out.println("Thread run()")// 省略接口名和方法名
).start();
```

使用Lambda表达式来充当匿名内部类则直接连接口名都直接省略了。当然对于函数内部有多行的就使用大括号围起来：

```java
new Thread(
        () -> {
            System.out.print("Hello");
            System.out.println("Zssaer");
        }
).start();
```

对于这种没有参数的类，Lambda表达式直接使用一个空括号表示。而对于带参数的则是：

```java
// JDK7 匿名内部类写法
List<String> list = Arrays.asList("I", "love", "you", "too");
Collections.sort(list, new Comparator<String>(){// 接口名
    @Override
    public int compare(String s1, String s2){// 方法名
        if(s1 == null)
            return -1;
        if(s2 == null)
            return 1;
        return s1.length()-s2.length();
    }
});

// JDK8 Lambda表达式写法
List<String> list = Arrays.asList("I", "love", "you", "too");
Collections.sort(list, (s1, s2) ->{// 省略参数表的类型
    if(s1 == null)
        return -1;
    if(s2 == null)
        return 1;
    return s1.length()-s2.length();
});
```

上述代码内部类重载了`Comparator`接口的`compare()`方法，而这个方法接受两个参数，所以自然在Lambda表达式中括号中以两个参数来呈现。

***注意：Lambda表达式中参数大多数情况下 不需要指定其类型***，这得益于`javac`的**类型推断**机制，编译器能够根据上下文信息推断出参数的类型，当然它也有判断失误的时候，所以有时候也需要手动来指定参数类型。

那么Lambda表达式可以任意使用吗？

答案是 不行。

学个ES6的人，可能会认为这和ES6差不多，但其实记住Java是强类语言，每个变量和对象都必需有明确的类型（比如，Integer不能被声明为String），而JavaScript的变量和参数时没有强制类型这一说法（TypeScript则引入了变量类型）。

也正是Java是强类语言，所以**Lambda表达式的使用条件则是必须有相应的函数接口**。

比如一个类有两个含有两个参数的方法，一个含有一个参数的方法。Lambda表达式使用其中一个带两个参数的方法话，则需要精准到方法参数的类型上，而使用一个参数的方法的话，则不需要指定方法类型。

但如果当两个 含有两个参数的方法的参数都一样的 话...那么javac类型推断机制则没法判断到指定的那个方法，所以这种情况下就不能使用Lambda表达式。

也就是说你并不能在代码的任何地方任性的写Lambda表达式。



## Lambda表达式与匿名内部类区别

看了前面，可能大伙会理所应当认为Lambda表达式是匿名内部类的简写方式，一种新的语法糖而已。但其实并非那样，在JVM层面，Lambda表达式与匿名内部类有大区别：

通过Java编译后，匿名内部类的部分依旧是编译成了一个类，这个类的名称是编译器自动取名的。

而使用Lambda表达式的话，编译器则不会生成一个类。因为Lambda表达式是通过**invokedynamic**指令来实现的，通过反射原理，来进行映射出一个方法行为来实现其内容。

那么自然，由于Lambda表达式不是通过生成类来实现，那么自然如果在Lambda表达式中调用`this`引用自然是调用的是外部类的对象。而匿名内部类则不是那样。

```java
public class Hello {
	Runnable r1 = () -> { System.out.println(this); };
	Runnable r2 = () -> { System.out.println(toString()); };
	public static void main(String[] args) {
		new Hello().r1.run();
		new Hello().r2.run();
	}
	public String toString() { return "Hello Hoolee"; }
}
```

## 函数接口

JDK8中在引入Lambda表达式时，同时也也在Collections中新增了java.util.function包，也就是我们常说的函数接口，这是Lambda表达式的基础。这也表明了从JDK8开始Java支持使用函数来进行编程的思想。

JDK8主要有 函数接口：Funtion、Consumer、Supplier、UnaryOperator、BiConsumer

### Funtion - 接受一个参数，返回一个对象

Funtion函数接口接收一个泛型T对象，并且返回泛型R对象。

```java
Function<Integer, String> func1 = a -> String.valueOf(a);
String s = func1.apply(12);

Function<String, user> func2 = (str) -> new user(str);
User user = func2.apply("zzz");
```

它使用accept进行调用，调用的参数等于函数传入的参数。这里需要记住的是，泛型第一个类型是入参类型，第二个类型是返回类型



### Consumer - 接受一个参数，返回空值

Consumer函数接口接收一个泛型的参数T，返回空。

```java
Consumer<String> consumer = t -> System.out.println(t);
consumer.accept("ssss");
// 使用方法引用快速创建
Consumer<String> consumer2 = System.out::println;
consumer2.accept("qwe");
// 多行内容函数
Consumer<Integer> consumer = x -> {
        int a = x + 2;
        System.out.println(a);// 12
        System.out.println(a + "_");// 12_
};
consumer.accept(10);
```

其中它的泛型就是其该函数的接受参数的类型。它使用accept进行调用，调用的参数等于函数传入的参数。

Consumer函数主要用于对入参做一些列的操作，比如用在 stream中的forEach。



### supplier - 无接受参数，返回一个类型

supplier顾名思义，供应商。它没有不需要入参，接收一个泛型参数T，表示其返回类型。

```java
Supplier<Emp> supplierEmp = Emp::new;
Emp emp = supplierEmp.get();
```

supplier和上面两个函数接口不一样的是，它只有一个get方法，表示返回。



### UnaryOperator - 接受一个参数，返回同类型参数

UnaryOperator函数接口继承了Function接口，定义了一个apply的抽象类，接收一个泛型T对象，并且返回泛型T对象，也就是说 入参和回参是一个类型。

```java
UnaryOperator<String> ddb = x -> x + 1;
String text = ddb.apply("aa"));// aa1
```

因为UnaryOperator，入参和返回值是同一个类型，所以通常在修改、添加业务中使用。



### BiConsumer - 接受两个参数，返回空值

BiConsumer函数接口是接受两个入参的Consumer，同样都是空返回值。

```java
// 声明函数对象 consumer
BiConsumer<String,String> consumer = (str1, str2) -> {
    // 拼接字符串
    sb.append(str1);
    sb.append(str2);
};
// 调用Consumer.accept()方法接收参数
consumer.accept("我是参数01","，我是参数02。我们被BiConsumer.accept(T,V)接收并处理了");
```



















