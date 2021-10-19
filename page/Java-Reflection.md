# Java-Reflection(JAVA反射)

## 定义

**Java-Reflection（JAVA反射机制）是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够通过Java-Reflection来调用它的任意方法和属性（不管是公共的还是私有的）；这种动态获取信息以及动态调用对象方法的功能被称为java的反射机制。**

## 用途

与普通的类编译静态生成不一样的是，Java-Reflection能够动态的生成类，所以Java-Reflection是Java框架开发的重要的一部分，很多优秀的开源框架都是通过反射完成的。

Java-Reflection可以在在日常的第三方应用开发过程中，会遇到某个类的某个成员变量、方法或是属性是私有的或是只对系统应用开放，这时候就可以利用Java的反射机制通过反射来获取所需的私有成员或是方法。

## 种类

与Java-Reflection相关种类主要分为：

| 类名          | 用途                                             |
| ------------- | ------------------------------------------------ |
| Class类       | 代表类的实体，在运行的Java应用程序中表示类和接口 |
| Field类       | 代表类的成员变量（成员变量也称为类的属性）       |
| Method类      | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |
| Annotation类  | 代表类的注解                                     |

## 基础技术

### Class类

#### **获得类相关的方法**

| 方法                                          | 用途                                                   |
| --------------------------------------------- | ------------------------------------------------------ |
| asSubclass(Class<U> clazz)                    | 把传递的类的对象转换成代表其子类的对象                 |
| Cast                                          | 把对象转换成代表类或是接口的对象                       |
| getClassLoader()                              | 获得类的加载器                                         |
| getClasses()                                  | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses()                          | 返回一个数组，数组中包含该类中所有类和接口类的对象     |
| forName(String className)                     | 根据类名返回类的对象                                   |
| getName()                                     | 获得类的完整路径名字                                   |
| newInstance()                                 | 创建类的实例                                           |
| getPackage()                                  | 获得类的包                                             |
| getSimpleName()                               | 获得类的名字                                           |
| getSuperclass()                               | 获得当前类继承的父类的名字                             |
| getInterfaces()                               | 获得当前类实现的类或是接口                             |
| getAnnotation(Class<T> annotationClass)       | 获取类上面的注解                                       |
| isAnnotationPresent(Class<T> annotationClass) | 判断是否对应的注解                                     |

例：使用forName来创建一个指定类的实例：

```java
// aClass实例相当于Consumer实例
Class aClass = Class.forName("com.Dan.Consumer");
Object o =aClass.newInstance();
```

#### **获得类中属性相关的方法**

| 方法                          | 用途                   |
| ----------------------------- | ---------------------- |
| getField(String name)         | 获得某个公有的属性对象 |
| getFields()                   | 获得所有公有的属性对象 |
| getDeclaredField(String name) | 获得某个属性对象       |
| getDeclaredFields()           | 获得所有属性对象       |

例：获取一个类中的私有对象：

```java
Field field = c4.getDeclaredField("name");
String name = field.get(o);
```

#### **获得类中构造器相关的方法**

| 方法                                               | 用途                                   |
| -------------------------------------------------- | -------------------------------------- |
| getConstructor(Class...<?> parameterTypes)         | 获得该类中与参数类型匹配的公有构造方法 |
| getConstructors()                                  | 获得该类的所有公有构造方法             |
| getDeclaredConstructor(Class...<?> parameterTypes) | 获得该类中与参数类型匹配的构造方法     |
| getDeclaredConstructors()                          | 获得该类所有构造方法                   |

例：获取一个类中的某个私有构造函数：

```java
Class[] p = {String.class};
constructors = c4.getDeclaredConstructor(p)
```

#### **获得类中方法相关的方法**

| 方法                                                       | 用途                   |
| ---------------------------------------------------------- | ---------------------- |
| getMethod(String name, Class...<?> parameterTypes)         | 获得该类某个公有的方法 |
| getMethods()                                               | 获得该类所有公有的方法 |
| getDeclaredMethod(String name, Class...<?> parameterTypes) | 获得该类某个方法       |
| getDeclaredMethods()                                       | 获得该类所有方法       |

例：获取一个类中的某个私有方法：

```java
Class[] p4 = {String.class};
Method method = c4.getDeclaredMethod("welcome",p4);
```



###  Field类

Field类的成员变量（成员变量也称为类的属性）。

| 方法                                          | 用途                        |
| --------------------------------------------- | --------------------------- |
| equals(Object obj)                            | 属性与obj相等则返回true     |
| get(Object obj)                               | 获得obj中对应的属性值       |
| set(Object obj, Object value)                 | 设置obj中对应属性值         |
| getType()                                     | 返回属性声明类型的class对象 |
| getGenericType()                              | 返回属性声明类型的Type对象  |
| getAnnotation(Class<T> annotationClass)       | 获取变量\属性上面的注解     |
| isAnnotationPresent(Class<T> annotationClass) | 判断是否对应的注解          |

例：设置一个实例中的某个对象/属性的值：

```java
Class c1 = Class.forName("com.test.bean.Xxx");
Object o = c1.newInstance();
Field field2 = c1.getField("sex");

field2.set(o, 1);
```



注意，其中getType() 和 getGenericType()的区别 ：

1. 首先是返回的类型不一样，一个是Class对象一个是Type接口。
2. 如果属性是一个泛型，从getType（）只能得到这个属性的接口类型。但从getGenericType（）还能得到这个泛型的参数类型。
3. getGenericType（）如果当前属性有签名属性类型就返回，否则就返回 Field.getType()。



### Constructor类

Constructor代表类的构造方法。

| 方法                                          | 用途                       |
| --------------------------------------------- | -------------------------- |
| newInstance(Object... initargs)               | 根据传递的参数创建类的对象 |
| getAnnotation(Class<T> annotationClass)       | 获取构造函数上面的注解     |
| isAnnotationPresent(Class<T> annotationClass) | 判断是否对应的注解         |

例：使用一个类的构造方法来创建实例：

```java
Class c2 = Class.forName("com.test.bean.Xxx");
Class[] p = {String.class, Integer.class, Integer.class, String.class};
Constructor constructor1 = c2.getConstructor(p);

Object o1 = constructor1.newInstance("码云", 1, 57, "资本家");
```



### Method类

Method代表类的方法。

| 方法                                          | 用途                                     |
| --------------------------------------------- | ---------------------------------------- |
| invoke(Object obj, Object... args)            | 传递object对象及参数调用该对象对应的方法 |
| getParameterTypes()                           | 获取方法所有的参数类型                   |
| getReturnType()                               | 获取方法返回对象的类型                   |
| getParameterTypes()                           | 返回方法中参数对象的Class类型            |
| getGenericParameterTypes()                    | 返回方法中参数对象的Type类型             |
| getAnnotation(Class<T> annotationClass)       | 获取方法上面的注解                       |
| isAnnotationPresent(Class<T> annotationClass) | 判断是否对应的注解                       |

例：使用一个 **类的方法类** 来 调用某个类的**实例**中的方法：

```java
Class c2 = Class.forName("com.test.bean.Xxx");

Method method1 = c2.getMethod("sout");
method.invoke(o1);
```



注意，其中getParameterTypes()和getGenericParameterTypes()的区别：

1. 首先是返回的类型不一样，一个是Class对象一个是Type接口。
2. 如果属性是一个泛型，从getParameterTypes（）只能得到这个属性的接口类型。但从getGenericParameterTypes（）还能得到这个泛型的参数类型。



### 公共属性和方法

Field类、Constructor类、Method类等等都具有一些公共属性和方法：

| 方法                        | 用途                                        |
| --------------------------- | ------------------------------------------- |
| setAccessible(Boolean flag) | 设置可访问性                                |
| getModifiers()              | 返回此对象表示的可执行文件的 Java修饰符常量 |
| getName()                   | 返回该类型对象的名称                        |

对于私有属性和私有构造器、私有方法，如果需要进行反射设置、修改内容必须先要设置其可访问性，否则会抛出`IllegalAccessException`异常

```java
method1.setAccessible(true);
```



其中Class、和Constructor都拥有一个`newInstance`方法，都可以用来实例化对象。但是:

**Class.newInstance() 只能够调用无参的构造函数，即默认的构造函数； 如果该类的无参构造已经被私有化则会抛出IllegalAccessException异常
Constructor.newInstance() 可以根据传入的参数，调用任意构造构造函数。** 

大部分时间，虽然使用Class.newInstance方法创建反射实例非常方便，但是为了容错性考虑，推荐使用Constractor.newInstance方法。



## 详解技术

上面只是简单的了解和应用了下Java反射的能力，要进一步进行应用，就得学习一些详细的内容。



### 一.泛型

1、声明一个需要被参数化（parameterizable）的类/接口。
2、使用一个参数化类。

当你声明一个类或者接口的时候你可以指明这个类或接口可以被参数化，java.util.List接口就是典型的例子。你可以运用泛型机制创建一个标明存储的是String类型list，这样比你创建一个Object的list要更好，`new ArrayList<String>()`。

你不能在**运行期获知一个被参数化的类型的具体参数类型是什么**，但是你可以在用到这个被参数化类型的方法以及变量中找到他们，换句话说就是获知他们具体的参数化类型，所以看看Java-Reflection中泛型的使用问题。

#### 方法返回的泛型类型

如果你获得了`java.lang.reflect.Method`对象，那么你就可以获取到这个方法的返回类型信息。如果方法返回一个带有泛型类那么你就可以获得这个泛型类的具体参数化类型,而不是仅仅只是一个类。

比如这儿定义了一个类这个类:

```java
  public class MyClass {

  protected List<String> stringList = ...;

  public List<String> getStringList(){
    return this.stringList;
  }
}
```

我们可以获取getStringList()方法的泛型返回类型，换句话说，我们可以检测到getStringList()方法返回的是`List<String>`而不仅仅只是一个`List`。

```java
Method method = MyClass.class.getMethod("getStringList", null);
// 返回方法返回对象类型
Type returnType = method.getGenericReturnType();
// 比较是否为参数化类型
if(returnType instanceof ParameterizedType){
    ParameterizedType type = (ParameterizedType) returnType;
    Type[] typeArguments = type.getActualTypeArguments();
    for(Type typeArgument : typeArguments){
        Class typeArgClass = (Class) typeArgument;
        System.out.println("typeArgClass = " + typeArgClass);
        // 打印结果:"typeArgClass = java.lang.String"
    }
}
```

其中的`ParameterizedType` 是`Type`的子类，为参数化类型，就是带有类型参数的类型，即常说的泛型，如：List<T>、Map<Integer, String>、List<? extends Number>。

而`getActualTypeArguments()`方法为返回表示此类型【实际类型参数】的 Type 对象的数组，也就是其中的泛型对象。

#### 方法参数中的泛型类型

当然同样可以通过反射来获取方法参数的泛型类型：

比如将其上面的方法修改一些：

```java
public void setStringList(List<String> list){
    this.stringList = list;
  }
```

那么，可以像这样来获取方法的参数中的泛型参数：

```java
method = Myclass.class.getMethod("setStringList", List.class);
// 返回方法参数类型
Type[] genericParameterTypes = method.getGenericParameterTypes();

for(Type genericParameterType : genericParameterTypes){
    if(genericParameterType instanceof ParameterizedType){
        ParameterizedType aType = (ParameterizedType) genericParameterType;
        Type[] parameterArgTypes = aType.getActualTypeArguments();
        for(Type parameterArgType : parameterArgTypes){
            Class parameterArgClass = (Class) parameterArgType;
            System.out.println("parameterArgClass = " + parameterArgClass);
        }
    }
}
```

其实和上方返回泛型获取相似，只是方法变了。

#### 类中变量泛型类型

上面都是说方法中的泛型，说说类上的变量、属性的泛型：

一个定义了一个名为stringList的成员变量的类。

```java
public class MyClass {
  public List<String> stringList = ...;
}
```

获取其中变量`stringList`返回的泛型类型

```java
Field field = MyClass.class.getField("stringList");

Type genericFieldType = field.getGenericType();

if(genericFieldType instanceof ParameterizedType){
    ParameterizedType aType = (ParameterizedType) genericFieldType;
    Type[] fieldArgTypes = aType.getActualTypeArguments();
    for(Type fieldArgType : fieldArgTypes){
        Class fieldArgClass = (Class) fieldArgType;
        System.out.println("fieldArgClass = " + fieldArgClass);
    }
}
```



### 二.注解

注解是Java 5的一个新特性。注解是插入你代码中的一种注释或者说是一种元数据（meta data）。这些注解信息可以在编译期使用预编译工具进行处理（pre-compiler tools），也可以在运行期使用Java反射机制进行处理。下面是一个类注解的例子：

```java
@MyAnnotation(name="someName",  value = "Hello World")
public class TheClass {
}
```

下面是MyAnnotation注解的定义：	

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)

public @interface MyAnnotation {
  public String name();
  public String value();
}
```

其中简单解析一下这个注解上面内容:

@Retention(RetentionPolicy.RUNTIME)表示这个注解存在于JAVA虚拟机运行期，可以在运行期通过反射访问。如果你没有在注解定义的时候使用这个指示那么这个注解的信息不会保留到运行期，那么这样反射就无法获取它的信息。
@Target(ElementType.TYPE) 表示这个注解只能用在类型上面（比如类跟接口）。你同样可以把Type改为Field或者Method，或者你可以不用这个指示，这样的话你的注解在类，方法和变量上就都可以使用了。

#### 获取类上的注解

通过反射可以获取类上的注解信息：

```java
 Class aClass = TheClass.class;
Annotation[] annotations = aClass.getAnnotations();

for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("name: " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
}
```

对于方法上、构造函数上、属性变量上的获取反射都类似。

#### 获取方法参数的注解

对于方法的参数也可以进行注解：

```java
public class TheClass {
  public static void doSomethingElse(
        @MyAnnotation(name="aName", value="aValue") String parameter){
  }
}
```

通过反射可以获取方法参数上的注解信息：

```java
Method method = ... //获取方法对象
Annotation[][] parameterAnnotations = method.getParameterAnnotations();
Class[] parameterTypes = method.getParameterTypes();

int i=0;
for(Annotation[] annotations : parameterAnnotations){
  Class parameterType = parameterTypes[i++];

  for(Annotation annotation : annotations){
    if(annotation instanceof MyAnnotation){
        MyAnnotation myAnnotation = (MyAnnotation) annotation;
        System.out.println("param: " + parameterType.getName());
        System.out.println("name : " + myAnnotation.name());
        System.out.println("value: " + myAnnotation.value());
    }
  }
}
```

需要注意的是Method.getParameterAnnotations()方法返回一个注解类型的二维数组，每一个方法的参数包含一个注解数组。



### 三.数组

利用反射机制来处理数组会有点棘手。

尤其是当你想要获得一个数组的Class对象，比如int[]等等。

其中Java-Reflection中需要通过`java.lang.reflect.Array`这个类来处理数值。注意不要把`java.util.Arrays`与其混淆。`java.util.Arrays`是一个提供了遍历数组，将数组转化为集合等工具方法的类。



#### 反射创建/访问数组

Java反射机制通过java.lang.reflect.Array类来创建数组。

```java
int``[] intArray = (``int``[]) Array.newInstance(``int``.``class``, ``3``);
```

Array.newInstance()方法的第一个参数表示了我们要创建一个什么类型的数组。第二个参数表示了这个数组的空间是多大。

通过Java反射机制同样可以访问数组中的元素。具体可以使用Array.get(…)和Array.set(…)方法来访问。

```java
Array.set(intArray, 0, 123);
Array.set(intArray, 1, 456);
Array.set(intArray, 2, 789);

System.out.println("intArray[0] = " + Array.get(intArray, 0));
System.out.println("intArray[1] = " + Array.get(intArray, 1));
System.out.println("intArray[2] = " + Array.get(intArray, 2));
```



#### 获取数组Class对象

正确情况下，相较于普通对象来说，数组的Class对象获取起来有些特殊：

```java
Class intArray = Class.forName(``"[I"``);
```

上面便是获取int数组的Class对象。在JVM中字母I代表int类型，左边的‘[’代表我想要的是一个int类型的数组，这个规则同样适用于其他的原生数据类型。

而对于普通对象类型的数组有一点细微的不同，比如说字符串数组：

```java
Class stringArrayClass = Class.forName(``"[Ljava.lang.String;"``);
```

注意‘[L’的右边是类名，类名的右边是一个‘;’符号。这个的含义是一个指定类型的数组。



对于普通原生数据类型，你不能通过名称直接得到：

```java
// 直接获取的化，这样都会抛出ClassNotFoundException
Class intClass1 = Class.forName("I");
Class intClass2 = Class.forName("int");
```

不通过反射的话你可以这样来获取数组的Class对象：

```java
Class stringArrayClass = String[].``class``;
```

结合情况，通常会用下面这个辅助方法来获取普通对象以及原生对象的Class对象：

```java
public Class getClass(String className){
  if("int" .equals(className)) return int .class;
  if("long".equals(className)) return long.class;
  ...
  return Class.forName(className);
}
...
Class theClass = getClass(theClassName);
    
```

这是一个特别的方式来获取指定类型的指定数组的Class对象。无需使用类名或其他方式来获取这个Class对象。



## 高级技术

Java的反射不仅仅只是简单的进行类的操作和模拟，一些动态技术也是属于Java反射实现的。下面来进行讲解更高级、复杂的技术点。



### 一.动态类的加载和重载

Java允许你在JVM（JAVA虚拟机）中运行期动态加载和重载类，但是这个功能并没有像人们希望的那么简单直接。

需要一题的是：JAVA加载类`ClassLoader`不属于Java反射API，而Java动态类加载特性是Java反射机制的一部分而不是Java核心平台的一部分。

了解动态类的加载和重载，之前我们需要先了解一些相关内容：

#### 前置内容

#####  1.类加载器

所有Java应用中的类都是被`java.lang.ClassLoader`类的一系列子类加载的。

因此要想动态加载类的话也必须使用`java.lang.ClassLoader`的子类。

一个类一旦被加载时，这个类引用的所有类也同时会被加载。所以类加载过程是一个递归的模式，所有相关的类都会被加载。但并不一定是一个应用里面所有类都会被加载，与这个被加载类的引用链无关的类是不会被加载的，直到有引用关系的时候它们才会被加载。

##### 2.类加载顺序

在Java中类的加载是一个有序的顺序。当你新创建一个标准的Java类加载器时你必须提供它的父加载器。

当一个类加载器被调用来加载一个类的时候，首先会调用这个加载器的父加载器来加载。如果从父加载器无法找到这个类，这时候这个加载器才会尝试去加载这个类。所以加载类的时候是优先从父加载器来加载，然后在考虑自己的加载器。

> 所以类加载器 加载类的顺序如下：
> 1、检查这个类是否已经被加载。
> 2、如果没有被加载，则首先调用父加载器加载。
> 3、如果父加载器不能加载这个类，则尝试加载这个类。

当然当你实现一个有***重载类***功能的类加载器，它的顺序与上述会有些不同。类重载不会请求的他的父加载器来进行加载。



#### 动态类的加载

动态加载一个类十分简单。你要做的就是获取一个类加载器然后调用它的loadClass()方法。下面是个例子：

```java
public class MainClass {
  public static void main(String[] args){
    ClassLoader classLoader = MainClass.class.getClassLoader();
    try {
        Class aClass = classLoader.loadClass("com.jenkov.MyClass");
        System.out.println("aClass.getName() = " + aClass.getName());
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    }
}
```

这里获取了启动类的类加载器，然后调用类加载器的loadClass方法，通过包名来动态加载了一个类。

> 这里大伙可能会问，“使用类加载器的loadClass方法加载类，和Class类的forName方法获取类有什么不一样呢？不都是返回了一个对于类的Class对象吗？”
>
> 这里说明下两者的区别：
>
> - Class.forName(className)方法，内部实际调用的方法是  Class.forName(className,true,classloader);
>   第2个boolean参数表示类是否需要初始化，  Class.forName(className)默认是需要初始化。一旦初始化，就会触发目标类对象的 static块代码执行，static参数也也会被再次初始化。
> - ClassLoader.loadClass(className)方法，内部实际调用的方法是  ClassLoader.loadClass(className,false);
>   第2个 boolean参数，表示目标对象是否进行链接，false表示不进行链接，不进行链接意味着不进行包括初始化等一些列步骤，那么静态块和静态对象就不会得到执行
>
> **所以Class.forName(className) 是初始化加载类，而ClassLoader.loadClass(className)方法只是加载类，不会初始化。**



#### 动态类的重载

相比于动态类加载，动态类的重载会显得复杂些。

Java内置的类加载器在加载一个类之前会检查它是否已经被加载，如果被加载将会直接去获取那个类，而不是重新加载。因此重载一个类是无法使用Java内置的类加载器的，如果想要实现重载一个类的话，你需要手动继承ClassLoader定义一个自己的加载器子类。

除此之外，所有被加载的类都需要被链接。这个过程是通过ClassLoader.resolve()方法来完成的。由于这是一个final方法，因此这个方法在ClassLoader的子类中是无法被重写的。resolve()方法是不会允许给定的ClassLoader实例链接一个类两次。所以每当你想要重载一个类的时候你都需要New一个新的ClassLoader的子类。

##### 自定义类重载

上面说了，不能使用已经加载过类的类加载器来重载一个类。因此你需要其他的ClassLoader实例来重载这个类。

但是大伙或许知道，JAVA应用中的类都是使用类的全名（包名 + 类名）作为一个唯一标识来让ClassLoader加载的，这意味着，类1被类加载器A加载，如果类加载器B又加载了类1，那么两个加载器加载出来的类1其实是不同的，相当于new出来的。这就与重载（重新加载）的概念不同了...

所以要到达重载的效果，就需要将加载类进行继承了一个超类并且也实现了一个接口。

```java
public class MyObject extends MyObjectSuperClass implements AnInterface2{
    //... body of class ... override superclass methods
    //    or implement interface methods
}
```

设置一个自定义加载器，对loadClass方法进行重写，如果你想用来重载类的话你可能会设计很多加载器。并且你也不会像下面这样将需要加载的类的路径硬编码（hardcore）到你的代码中：

```java
public class MyClassLoader extends ClassLoader{

    public MyClassLoader(ClassLoader parent) {
        super(parent);
    }

    public Class loadClass(String name) throws ClassNotFoundException {
        if(!"reflection.MyObject".equals(name))
                return super.loadClass(name);

        try {
            String url = "file:C:/data/projects/tutorials/web/WEB-INF/" +
                            "classes/reflection/MyObject.class";
            URL myUrl = new URL(url);
            URLConnection connection = myUrl.openConnection();
            InputStream input = connection.getInputStream();
            ByteArrayOutputStream buffer = new ByteArrayOutputStream();
            int data = input.read();
            while(data != -1){
                buffer.write(data);
                data = input.read();
            }
            input.close();
            byte[] classData = buffer.toByteArray();
            return defineClass("reflection.MyObject",
                    classData, 0, classData.length);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

利用超类或者接口进行重载：

```java
public static void main(String[] args) throws
    ClassNotFoundException,
    IllegalAccessException,
    InstantiationException {

    ClassLoader parentClassLoader = MyClassLoader.class.getClassLoader();
    MyClassLoader classLoader = new MyClassLoader(parentClassLoader);
    Class myObjectClass = classLoader.loadClass("reflection.MyObject");

    AnInterface2       object1 =
            (AnInterface2) myObjectClass.newInstance();

    MyObjectSuperClass object2 =
            (MyObjectSuperClass) myObjectClass.newInstance();

    //重载类需要创建一个新的类加载器
    classLoader = new MyClassLoader(parentClassLoader);
    myObjectClass = classLoader.loadClass("reflection.MyObject");
        
    object1 = (AnInterface2)       myObjectClass.newInstance();
    object2 = (MyObjectSuperClass) myObjectClass.newInstance();
}
```



### 二.动态代理

#### 静态代理简介

在讲述动态代理前，我们先聊聊“静态代理”。

静态代理其实是Java中的一种设计模式，利用接口和实现类来实现进行扩展使用。

使用场景，打个比方：

某个卖票场景有两种模式，一种为线下，一种为线上，它们都有实现了一个售票接口，里面一种功能，“售票”。

大家都知道线上的肯定会比线下的贵（ps：不然房租不要钱的吗？零元购？）

所以在其他思路下，我们或许会单独进行重写“售票"功能，让其价格更改。

但在静态代理模式下，我们只需要实现该接口后，将起线上对象注入，然后再通过重写中调用线上对象然后，再额外算入XXX税等。

描述所得代码：

```java
interface Worker {
    // 售票;
    void sell();
}
/**
** 线上
*/
class online implements Worker {
    @Override
    public void sell() {
        System.out.println("需支付300元票费");
    }
}
/**
** 线下
*/
class Offline implements Worker {

    // 私有一个被代理类的父类引用,这样做是为了适应所有的被代理类对象,只要实现了接口就好;
    private Worker online;

    // 传入被代理类对象,这里的作用是初始化"代理类"中的"被代理类"对象;
    public Offline(Worker online) {
        this.online = online;
    }

    /**
     * 增强服务和功能;
     */
    @Override
    public void sell() {
        // 代理实现线上服务;
        online.sell();
        // 额外服务;
        noQueue();
    }

    // 代理类本身自带功能;
    public void noQueue() {
        System.out.println("需支付40元线下税");
    }
}
```

可能大伙会觉得这样做不是太麻烦了吗？直接重写方法不行吗？

但是静态代理模式主要是运用于某些特殊情况下，如当引入第三方依赖时，无法复写第三方Jar包的类时,又想进行实现扩展的话，那使用静态代理即可实现第三方包Jar包功能扩展。



#### 动态代理简介

了解了静态代理后，我们不能发现虽然静态代理带来扩展的优点，但是缺点不少：

- **可维护性低**。由于代理类和被代理类都实现了同一个接口，如果接口发生了更改，那么被代理类和所有的代理类都要进行修改，比如接口新增一个方法，那么所有的代理类和被代理类都要重写这个方法，这无疑增加了巨大的工作量。
- **可重用性低**。通过观察可以发现，代理类们的代码大体上其实是差不多的，但是由于个别的差异，导致我们不得不重新写一个新的代理类。

那么我们开始动态代理的讲解。

**动态代理 即 利用Java反射机制在运行期动态的创建接口的实现类的行为。**

创建个代理类来代替实际需要的类，利用这个代理来实现原类的功能，这一行为就叫做代理。而能够在其Java编译后在JVM（Java虚拟机）中运行时动态实现代理的，被称为动态代理。

其中 动态代理 的用途十分广泛，比如Spring AOP、Hibernate数据查询、测试框架的后端mock、RPC远程调用、Java注解对象获取、日志、用户鉴权、全局性异常处理、性能监控，甚至事务处理等都使用到了动态代理。

在这之前，我们得了解`InvocationHandler`-调用处理程序 和 Proxy类-代理类



#### InvocationHandler

InvocationHandler是一个接口类，里面只有一个方法，`invoke`，专门用来实现代理类的功能。

```java
public Object invoke(Object proxy, Method method, Object[] args)
    throws Throwable;
```

它接受三个参数：代理类，对象真实方法，传递参数。这个方法的效果和Method中的`invoke`类似。返回的Object即方法返回内容。

其中第一个参数，大部分时间动态代理不会使用。

所以要实现一个动态代理，就需要创建一个动态代理类的调用处理程序。而每一个动态代理类的调用处理程序都必须实现这个InvocationHandler接口，并且每个代理类的实例都关联到了实现该接口的 动态代理类调用处理程序 中。

当我们通过动态代理对象调用一个方法时候，这个方法的调用就会被转发到实现InvocationHandler接口类的invoke方法来调用，从而实现动态代理类。

创建一个自定义动态代理的调用处理程序类，例如：

```java
public class WorkHandler implements InvocationHandler{
    // 需要代理的类
    private Object obj;
    public WorkHandler() {
        // TODO Auto-generated constructor stub
    }
    // 构造函数
    public WorkHandler(Object obj) {
        this.obj = obj;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //在真实的对象执行之前我们可以添加自己的操作
        System.out.println("代理处理开始。。。");
        Object invoke = method.invoke(obj, args);
        //在真实的对象执行之后我们可以添加自己的操作
        System.out.println("方法代理完毕!");
        System.out.println("代理处理结束。。。");
        return invoke;
    }
}
```



#### 创建代理

创建代理前，了解下Proxy这个类。

Proxy指的是`java.lang.reflect.Proxy`，顾名思义，就是专门用作代理的。

它拥有很多方法，但其中我们最常用的是它的`newProxyInstance`方法。

你可以通过使用Proxy.newProxyInstance()方法创建动态代理。其中`newProxyInstance()`方法有三个参数：

```java
public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h)
```

> 1、loader：类加载器（ClassLoader），用来加载动态代理类。
> 2、interfaces：需要代理的类 的实现的接口。
> 3、h：一个InvocationHandler对象，将其代理方法分派到的调用处理程序。

如下例子：

```java
public interface Subject {
    public String SayHello(String name);
}
```



```java
public class RealSubject implements Subject{
    @Override
    public String SayHello(String name) {
        return "hello " + name;
    }

    @Override
    public String toString() {
        return "RealSubject{}";
    }
}
```

我们创建一个RealSubject的动态代理：

```java
public class ProxyMain {
    public static void main(String[] args) throws Exception {
        Subject real =new RealSubject();
        ClassLoader loader = ProxyMain.class.getClassLoader();

        // 创建一个InvocationHandler实例，用作处理代理逻辑
		InvocationHandler handler = new WorkHandler(real);
		// 创建代理
		Subject realSubjectProxy = (Subject)Proxy.newProxyInstance(loader, new Class[]{Subject.class}, handler);
    }
}
```

在执行完这段代码之后，变量RealSubjectProxy包含一个MyInterface接口的的动态实现。所有对RealSubjectProxy的调用都被转向到实现了InvocationHandler接口的handler上执行。

我们利用这个动态代理类`RealSubjectProxy`调用一下`RealSubject`的方法试试:

```java
System.out.println(realSubjectProxy.SayHello("我是一个代理类"));
```

结果输出：

```bash
代理处理开始。。。
方法代理完毕!
代理处理结束。。。
hello 我是一个代理类
```

所以由此可见，在调用代理类使用方法后，其调用将自动转到`InvocationHandler`实现类中的invoke方法中进行操作。

同时我们使用`getClass().getName()`获取一下这个动态代理对象的类名：

```java
System.out.println(realSubjectProxy.getClass().getName());
```

结果输出：

```bash
com.sun.proxy.$Proxy0
```

说明动态代理类对象依旧属于Proxy类，不属于原类。所以动态代理不会利用到原类进行操作，是完完全全以代理存在。

***注意：Proxy.newProxyInstance得到的动态代理类只能强转为 代理的类的接口 ，不能强转为 代理的类。因为JDK自带的动态代理必须实现接口，这也是JDK动态代理的缺点之一。***



#### 精简创建代理步骤

在了解了动态代理创建后，创建Proxy还是有点麻烦，所以我们可以精简下步骤：

修改自定义的InvocationHandler实例，新增一个bind方法，用作直接生成绑定后的动态代理类：

```java
/**
 * @description: TODO
 * @author: Zhaotianyi
 * @time: 2021/10/19 14:44
 */
public class WorkHandler implements InvocationHandler {
    // 需要代理的类
    private Object object;

    /**
     * 生成绑定后的动态代理类
     *
     * @param object 需要代理的类
     * @return Object 动态代理类
     */
    public Object bind(Object object) {
        this.object = object;
        return Proxy.newProxyInstance(this.object.getClass().getClassLoader(),
                this.object.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object returnObject = null;
        ...
        returnObject = method.invoke(object, args);
        ...
        return returnObject;
    }
}
```

在需要动态代理时，我们只需要创建WorkHandler并调用bind方法即可快速创建一个动态代理类。



#### 更多扩展选择

JAVA自带的反射`java.lang.reflect`包实现的动态代理，其目标类必须实现的某个接口，如果某个类没有实现接口则不能生成代理对象。所以这样的动态代理功能是比较单一的，目前第三方开源的**CGLIB**的代理则更为强大，被广泛应用于大型第三方框架中，如SpringAOP、Mybatis等。









