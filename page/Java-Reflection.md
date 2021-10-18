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

### 泛型

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



### 注解

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



### 数组

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



