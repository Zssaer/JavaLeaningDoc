# 快速创建一个自定义Spring Boot Starter

## 引言

相信大伙在搭建SpringBoot-JavaWeb项目时一定会使用许多第三方组件依赖包，其中大部分组件在选择时会选择其下的Spring-Boot-Starter依赖包。

因为使用其对应的SpringBoot-Starter包后可以在其SpringBoot配置中直接配置使用，十分方便，就拿Mysql为例，使用原版Mysql依赖包，需要创建一个XmL文件,按照其内部的XML解析器来进行配置Mysql配置，而在其使用SpringBoot-Mysql-Starter包，只需要在SpringBoot配置文件中进行简单配置即可，可谓是是否方便。

对于自己的开源项目来说，其实写一个spring-boot-starter，十分简单。

## 归纳需求

首先在开发一个starter前需要考虑哪些东西？
1、Maven 的Pom的配置
2、Configuration类：必须
	  Properties类：必须(用户动态的从配置文件读取配置)
	  Service类: 可以有不同定义

3、spring.factories 文件：必须，在resources/META-INF/spring.factories

## 创建Maven项目

在使用spring-boot-starter，会发现，有的项目名称是 XX-spring-boot-starter，有的是spring-boot-starter-XX，这个项目的名称有什么讲究呢？

SpringBoot官方是这么解释的：

> Do not start your module names with spring-boot, even if you use a different Maven groupId. We may offer official support for the thing you auto-configure in the future.
> As a rule of thumb, you should name a combined module after the starter.

翻译过来就是：

“不要使用spring-boot作为你的模块名，即使你使用不同的Maven groupId。我们（SpringBoot）可能会在将来为您自动配置的东西提供官方支持。根据经验，应该以启动器的名字命名一个组合模块。”

所以使用spring-boot-starter-XX是springboot官方的starter，XX-spring-boot-starter是第三方扩展的starter。那么我们自己所定义的starter的话就应该起名为XX-spring-boot-starter。

```xml
	<groupId>com.muggle</groupId>
    <artifactId>test-spring-boot-starter</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure</artifactId>
        </dependency>
        
        ...
       
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <!-- Import dependency management from Spring Boot -->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.0.6.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

我们这里使用的SpringBoot2.0.6.RELEASE版本，你可以自由换成新的版本。

其中我考虑到包的臃肿性，只是引用了2个SpringBoot的关键的包，`spring-boot-configuration-processor`和`spring-boot-autoconfigure`，一个是支持代码提示的依赖包，一个是自动化配置的依赖包。



## 定义Properties

我们需要定义一个Properties用作为SpringBoot配置文件的映射，实现读取到SpringBoot配置。

```java
@ConfigurationProperties(prefix = "helloworld")
public class HelloworldProperties {
  public static final String DEFAULT_WORDS = "world";、
  public

  private String words = DEFAULT_WORDS;

  public String getWords() {
    return words;
  }

  public void setWords(String words) {
    this.words = words;
  }
}
```

这里我们定义了一个Properties，用作获取SpringBoot的helloworld配置。项目启动时其中`helloworld.words`配置将自动装配到里面来，如果SpringBoot配置这个为空的话就是默认的“world”。



## 定义Configuration

Configuration是SpringBoot中一种重要的Bean，它本身在启动时会实现了自动注入，使用在Starter中用它来实现启动注入自己的相关服务。

```java
@Configuration
@ConditionalOnClass(TestService.class)
@EnableConfigurationProperties(HelloworldProperties.class)

public class AutoConfig {
    @AutoWired
    private HelloworldProperties properties;
    
    @Bean
    @ConditionalOnMissingBean(TestController.class)//当容器中没有指定Bean的情况下
    public TestService testService(){
        TestService testService = new TestService();
        testService.setWords(properties.getWords());
        return testService;
    }
}
```

我们这个Config中定义实现了一个TestService的服务类，它的实现基于其Properties配置而来。

并且将其TestService直接实现自动注入功能。



## 定义Factories文件

在正确情况下，要将某个类启动时自动调用的话，那就需要将其注入到IOC中。目前有2种方法：

第一种：在项目中启动类引入其类：

```java
...
import com.xxx.AutoConfig;
...
    
@SpringBootApplication
public class Application{
	...
}
```

第二种：使用spring.factories，在resource下新建`META-INF/spring.factories`内容为：

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=com.muggle.controller.AutoConfig
```

显然在Starter中不用定义启动类，应该使用第二种方式。



## 测试

我们在制作好starter可以进行测试一下，我们在一个其他项目中引入该项目的依赖：

```xml
<dependency>
	<groupId>xxx.xxx.xxx</groupId>
	<artifactId>xxx-starter</artifactId>
</dependency>
```

在SpringBoot配置中进行配置:

```xml-dtd
helloworld:
  words: 123
```

然后直接测试一下:

```java
public class DemoServiceTest extends StarterTestApplicationTests {
    @Autowired
    private TestService testService;
    @Test
    public void helloTest(){
        System.out.println(testService.getwords());
    }
}
```

成功返回:

```
Hello,123
```



就这样一个简易的Starter就做好了。













