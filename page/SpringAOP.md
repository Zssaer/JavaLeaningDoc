# Spring-AOP

## 介绍

AOP-全程"Aspect Orient Programming"，中文叫做“面向切面编程”，可以说它是OOP“面向对象编程”的补充和完善。

传统OOP思想中，引入封装、继承和多态性等概念来建立一种对象层次模型结构Class，允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系，要求在操作每一步中进行记录，这时利用OOP设计的话，就会在每一步中进行记录操作，从而导致大量的代码重复。

而AOP技术则恰恰相反，它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为“Aspect”，这种编程思想极大的减少了代码重复使用。

AOP思想和实现多用于日志记录、权限授权、事务处理等这样的行为前后执行内容上。

## 概念

全局：

* aspect（切面）：类似于OOP中的Class，一个Aspect存放一个系统功能的所有逻辑；在ApplicationContext中aop:aspect来配置；

* 连接点（Joinpoint）：程序执行过程中的某一事件，如方法被调用时、抛出异常时；**SpringAOP仅支持方法的连接点**，既仅能在方法调用前，方法调用后，方法抛出异常时等这些程序执行点进行操作。比如一个拥有两个方法的类，这两个方法都会是连接点。

  类似于OOP中类中的方法。

实际操作：

* 切入点（Pointcut）：类似于过滤器，一个表达式，用于**确定哪些类的哪些方法需要插入横切逻辑**；比如当某个连接点满足指定要求时，就可以被添加Advice，成为切入点。如何使用表达式定义切入点，是AOP的核心。
* 增强（Advice）：**切点上的具体执行逻辑**；Spring中有四种Advice：Before（前置）、After（后置）、Around（环绕）、After Throwing（抛出异常后）。
* 目标对象（Target）：被AOP框架进行增强处理的对象。如果是动态AOP的框架，则对象是一个被代理的对象。



## 基础使用

目前最流行的AspectJ，在Spring默认使用**AspectJ**切入点，需要导入依赖包：

```java
<!-- aspectjweaver -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
```

其中aspectjweaver是aspectj的织入包；一般简单AOP的话，只需要导入这个包即可。

### 一.创建切面类

创建一个Aspect组件类，并添加为`@Aspect`注解，表示该类为切面类。

```java
/**
 * @description: 普通切面类
 * @author: Zhaotianyi
 * @time: 2021/10/18 15:41
 */
@Aspect
@Component
public class CommonAspect {
    private Logger logger = LoggerFactory.getLogger(CommonAspect.class);
}
```



### 二.定义切入点

在其组件类中定义切入点（PointCut），用作于指定的XXX行为下。

```java
/**
 * 定义切入点,表示匹配com.admin.provider包及其子包下的所有类中的公共方法
 */
@Pointcut("execution(public * com.admin.provider..*.*(..))")
public void webLog(){
}
```

#### 切入点表达式

其中`@Pointcut`注解中的值为判断表达式，用来匹配指定方法，内容为:`表达标签 （ 表达式格式)`。

其中表达标签有：

> - execution()：用于直接匹配方法执行的连接点
> - args(): 用于匹配当前执行的方法传入的参数为指定类型的执行方法
> - this(): 用于匹配当前AOP代理对象类型的执行方法；注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配；
> - target(): 用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配；
> - within(): 用于匹配指定类型内的方法执行；
> - @args():于匹配当前执行的方法传入的参数持有指定注解的执行；
> - @target():用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解；
> - @within():用于匹配所以持有指定注解类型内的方法；
> - @annotation():用于匹配当前执行方法持有指定注解的方法；

其中`execution` 是用的最多的,而其表达式格式为:

```xml
modifier-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) throws-pattern?
```

> - modifier-pattern? 修饰符匹配，如public 表示匹配公有方法
>
> - ret-type-pattern 返回值匹配，* 表示任何返回值,全路径的类名等
>
> - declaring-type-pattern? 类路径匹配
>
> - name-pattern 方法名匹配，* 代表所有,set*,代表以set开头的所有方法
>
> - (param-pattern) 参数匹配，指定方法参数(声明的类型)
>
>   (..)代表所有参数
>
>   ()代表一个参数
>
>   (,String)代表第一个参数为任何值,第二个为String类型
>
> - throws-pattern? 异常类型匹配

其中带末尾?号的 modifiers-pattern?，declaring-type-pattern?，hrows-pattern?是可选项

> 例如:
>
> execution(public * *(..)) 定义任意公共方法的执行
> execution(* set*(..)) 定义任何一个以"set"开始的方法的执行
> execution(* com.xyz.service.AccountService.*(..)) 定义AccountService 接口的任意方法的执行
> execution(* com.xyz.service.*.*(..)) 定义在service包里的任意方法的执行
> execution(* com.xyz.service ..*.*(..)) 定义在service包和所有子包里的任意类的任意方法的执行

`@annotation`是方法注释的匹配,而`@within`和`@target`是对类注解的匹配:

> @annotation(org.springframework.transaction.annotation.Transactional) 表示带有@Transactional标注的任意方法.
>
> @within(org.springframework.transaction.annotation.Transactional) 表示带有@Transactional标注的所有类的任意方法.
>
> @target(org.springframework.transaction.annotation.Transactional) 表示带有@Transactional标注的所有类的任意方法.



### 三.定义增强

在其组件类中定义增强（Advice），用作每个切入点的执行逻辑。

其中Advice在AspectJ中类型有 before、after 和 around、After Throwing，分别表示“前置执行”、“后置执行”、“环绕执行”、“抛出异常后执行”等。

```java
/**
 * 切入点的前置执行
 */
@Before("webLog()")
public void doBefore(JoinPoint joinPoint) {
    // 执行的逻辑
}
```

上面定义了一个webLog()的切入点方法名 的前置执行Advice。

由上面的切入点定义，最终的结果为 `运行com.admin.provider包及其子包下的任一类的公共方法,都会提前执行doBefore中的逻辑行为`。

#### JoinPoint对象

其中你可能会注意到方法的函数有一个`JoinPoint`参数，这又是什么呢？

**JoinPoint**对象封装了SpringAop中切面方法的信息，通常用它来获取相关切入点的信息
**它的常用api:**

| 方法名                    | 功能                                                         |
| :------------------------ | :----------------------------------------------------------- |
| Signature getSignature(); | ***获取封装了署名信息的对象,在该对象中可以获取到目标方法名,所属类的Class等信息*** |
| Object[] getArgs();       | **获取传入目标方法的参数对象**                               |
| Object getTarget();       | **获取被代理的对象**                                         |
| Object getThis();         | **获取代理对象**                                             |

获取到的`Signature `通常用于跟踪或记录应用程序以获取有关连接点的反射信息。比如如下操作：

```java
Signature signature = joinPoint.getSignature();
System.out.println("目标方法名为:" + signature.getName());
System.out.println("目标方法所在的类的类名:" + signature.getDeclaringTypeName());
System.out.println("目标方法的访问修饰符:" + Modifier.toString(signature.getModifiers()));
```

通常这样得到的Signature可以强制转换为MethodSignature对象，`MethodSignature`相较于普通`Signature`对象,额外多了两个方法,可以方便直接获取方法：

| 方法名                | 功能                                  |
| :-------------------- | :------------------------------------ |
| Class getReturnType() | **获取切入点方法返回对象的类型Class** |
| Method getMethod();   | **获取切入点方法对象**                |



`ProceedingJoinPoint`对象是JoinPoint的子接口,该对象只能用在`@Around`的切面增强方法参数中。

还有一种特别的JointPoint对象，`ProceedingJoinPoint`对象是JoinPoint的子接口,该对象只能用在@Around的切面增强方法参数中。它相较于`ProceedingJoinPoint`添加了 两个不一样的方法:

| 方法名                        | 功能                         |
| :---------------------------- | :--------------------------- |
| Object proceed()              | 执行目标方法                 |
| Object proceed(Object[] var1) | 传入的新的参数去执行目标方法 |

要解析这里面的`执行目标方法`的话，则要先了解`@Around`这个切面增强：

`@Around`所谓环绕执行，它的作用为：

- **既可以在目标方法之前织入增强动作，也可以在执行目标方法之后织入增强动作；**
- **可以决定目标方法在什么时候执行，如何执行，甚至可以完全阻止目标目标方法的执行；**
- **可以改变执行目标方法的参数值，也可以改变执行目标方法之后的返回值； 所以当需要改变目标方法的返回值时，只能使用Around方法；**

**但是注意虽然Around功能强大，但通常需要在线程安全的环境下使用。**因此，如果使用普通的Before、AfterReturing增强方法就可以解决的事情，就没有必要使用Around增强处理了。



回顾过来，ProceedingJoinPoint中的proceed执行方法，即为执行切入点方法的内容。其返回值Object为切入点方法的返回值。在判断条件后可以强制转化。

```java
@Around("logPointCut()")
public Object around(ProceedingJoinPoint point) throws Throwable {
    long beginTime = System.currentTimeMillis();
    //执行方法
    Object result = point.proceed();
    String result1 = JSON.toJSONString(result);
    JSONObject jsonObject = JSON.parseObject(result1);
    //执行时长(毫秒)
    long time = System.currentTimeMillis() - beginTime;
    //保存日志
    saveSysLog(point, time, jsonObject);
    return result;
}
```

上面这个例截取于常见的日志记录AOP。

一个切入点可以有多个增强设置。

