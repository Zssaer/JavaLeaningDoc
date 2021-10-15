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

## 基础技术

### Class类

#### **获得类相关的方法**

| 方法                       | 用途                                                   |
| -------------------------- | ------------------------------------------------------ |
| asSubclass(Class<U> clazz) | 把传递的类的对象转换成代表其子类的对象                 |
| Cast                       | 把对象转换成代表类或是接口的对象                       |
| getClassLoader()           | 获得类的加载器                                         |
| getClasses()               | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses()       | 返回一个数组，数组中包含该类中所有类和接口类的对象     |
| forName(String className)  | 根据类名返回类的对象                                   |
| getName()                  | 获得类的完整路径名字                                   |
| newInstance()              | 创建类的实例                                           |
| getPackage()               | 获得类的包                                             |
| getSimpleName()            | 获得类的名字                                           |
| getSuperclass()            | 获得当前类继承的父类的名字                             |
| getInterfaces()            | 获得当前类实现的类或是接口                             |

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

| 方法                          | 用途                    |
| ----------------------------- | ----------------------- |
| equals(Object obj)            | 属性与obj相等则返回true |
| get(Object obj)               | 获得obj中对应的属性值   |
| set(Object obj, Object value) | 设置obj中对应属性值     |

例：设置一个实例中的某个对象/属性的值：

```java
Class c1 = Class.forName("com.test.bean.Xxx");
Object o = c1.newInstance();
Field field2 = c1.getField("sex");

field2.set(o, 1);
```



### Constructor类

Constructor代表类的构造方法。

| 方法                            | 用途                       |
| ------------------------------- | -------------------------- |
| newInstance(Object... initargs) | 根据传递的参数创建类的对象 |

例：使用一个类的构造方法来创建实例：

```java
Class c2 = Class.forName("com.test.bean.Xxx");
Class[] p = {String.class, Integer.class, Integer.class, String.class};
Constructor constructor1 = c2.getConstructor(p);

Object o1 = constructor1.newInstance("码云", 1, 57, "资本家");
```



### Method类

Method代表类的方法。

| 方法                               | 用途                                     |
| ---------------------------------- | ---------------------------------------- |
| invoke(Object obj, Object... args) | 传递object对象及参数调用该对象对应的方法 |
| getParameterTypes()                | 获取方法所有的参数类型                   |
| getReturnType()                    | 获取方法返回对象的类型                   |

例：使用一个 **类的方法类** 来 调用某个类的**实例**中的方法：

```java
Class c2 = Class.forName("com.test.bean.Xxx");

Method method1 = c2.getMethod("sout");
method.invoke(o1);
```



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

## 类中变量泛型类型

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

