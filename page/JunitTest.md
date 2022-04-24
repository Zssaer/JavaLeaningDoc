# JUnit测试

## 前言

JUnit是一个Java语言的单元测试框架。它由肯特·贝克和埃里希·伽玛（Erich Gamma）建立，逐渐成为源于Kent Beck的sUnit的xUnit家族中为最成功的一个。 JUnit有它自己的JUnit扩展生态圈。

JUnit是目前Java环境下最常用的单元测试框架，几乎项目中缺少不了。目前JUnit已经发展了5个版本了，其中JUnit4被广泛使用。



本文详细的记录了SpringBoot如何结合Junit写**测试用例，如何执行，打包执行，忽略执行**等操作，SpringBoot内置了Junit测试组件，使用很方便，不用再单独引入其他测试组件。

## POM依赖包

目前JUnit已经在SpringBoot中被官方Test架包收入，只需要导入SpringBoot官方的Test-starter包即可。

```xml
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-test</artifactId>
         <version>xxx</version>
		<scope>test</scope>
	</dependency>
```

其中<scope>中限的了该依赖包被限制用于项目中的test文件夹下。

**注意：这里需要说明的是不同的SpringBoot的版本下的test包分别使用不同的JUnit版本。其中SpringBoot 2.2.x版本以下的引入的是Junit4的测试版本，2.2.x及以上引入的是Junit5的版本。JUnit 4和JUnit 5之间有大的变化，所以使用方法没法兼容。这里我们主要说明JUnit4的版本，其中最后一个spring-boot-starter-test是2.1.9.RELEASE。**

使用spingbootTest单元测试的时候，注意以下版本号，避免不必要的异常错误。

## 快速创建单元测试文件

有了JUnit依赖自然就可以写单元测试文件了，它们一般位于项目的src下的test文件夹下。

这里我们不使用手动方式来进行创建单元测试，因为这样对于一些项目动辄数百个模块来说是相当的低效率。

我们选择使用第三方插件来进行批量生成对应的单元测试文件。

这样的自动化生成创建有很多选择，这里使用Idea的明星插件 -《JUnitGenerator》V2.0。

![](../picture/20220424173011.png)

安装后，我们在项目中单元随便选个需要测试的模块类，按`alt`+`Insert`，Idea弹出生成下拉框，选择`JUnit Test` 后点击JUnit4，创建一个对应的JUnit4测试文件。

![](../picture/20220424173507.png)

JUnitGenerator便会自动在项目的src下的test文件夹下自动生成对应的测试文件，并且在test文件夹下创建对应相同的结构树目录。

![](../picture/20220424174117.png)

这里需要注意JUnitGenerator生成的文件会根据系统的字节编码来设置，中文系统下会默认为GBK编码，只需要选择将文件编码设置回项目编码即可。

后续测试操作，我们根据这个测试文档来进行编辑即可。

## SpringBoot下测试文件配置

上面的测试文档虽然被自动生成下来了，但是并不能代表它能在SpringBoot下进行测试运行。

所以为了确保测试文件在SpringBoot中正常运行，所以需要在测试文件类上添加以下注解：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class XXXTest {
	...
}
```

其中@SpringBootTest这个注解在SpringBoot 2.2.x以下版本还需要指定一个项目启动类，通常还需要在test下创建一个项目启动类来进行连接。但是在2.2.x及以上只需要**一个**注解即可，它会自动设置到main下的项目启动类上。

## JUnit注释

上面创建了JUnit测试文件了，里面出现了一些杂七杂八的注解，为了后续编写测试文件，务必需要了解下JUnit的注解。

- @Test：将一个方法标记为测试方法；
- @Before：每一个测试方法调用前必执行的方法；
- @After：每一个测试方法调用后必执行的方法；
- @BeforeClass：所有测试方法调用前执行一次，在测试类没有实例化之前就已被加载，需用[static](https://so.csdn.net/so/search?q=static&spm=1001.2101.3001.7020)修饰；
- @AfterClass：所有测试方法调用后执行一次，在测试类没有实例化之前就已被加载，需用static修饰；
- @Ignore：暂不执行该方法；

这些注解均使用在方法上面，它们拥有不同的执行顺序。

直接运行整个测试类的话，顺序分别是：

@BeforeClass -> @Before -> @Test -> @After -> @AfterClass;

单个运行一个测试方法的话，顺序分别是：

@Before -> @Test -> @After;

