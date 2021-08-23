# SpringCloud与Dubbo融合使用

## 简介

Dubbo，是阿里巴巴服务化治理的核心框架，并被广泛应用于阿里巴巴集团的各成员站点。阿里巴巴近几年对开源社区的贡献不论在国内还是国外都是引人注目的，比如：JStorm捐赠给Apache并加入Apache基金会等。

Dubbo只是实现了服务治理，而Spring Cloud下面有17个子项目（可能还会新增）分别覆盖了微服务架构下的方方面面，服务治理只是其中的一个方面，一定程度来说，Dubbo只是Spring Cloud Netflix中的一个子集。但是在选择框架上，方案完整度恰恰是一个需要重点关注的内容。

由于Dubbo在国内有着非常大的用户群体，但是其周边设施与组件相对来说并不那么完善。很多开发者用户又很希望享受Spring Cloud的生态，因此也会有一些Spring Cloud与Dubbo一起使用的案例与方法出现，但由于Dubbo的设计中的注册中心服务采用了ZooKeeper，而开始时Spring Cloud体系中的注册中心并不支持ZooKeeper，即使到后来支持上了，但由于服务信息的粒度与存储也不一致。所以，长期以来，在服务治理层面上，SpringCloud与Dubbo都是分开隔离使用的。

但如今SpringCloud-Alibaba的出现，重新编写了Dubbo的设计和注册中心，所以SpringCloud-Alibaba可以与Dubbo进行融合使用。

利用SpringCloud-Alibaba组件中的Nacos做服务注册中心，使用用Dubbo来进行实现接口RPC方案。从此使传统的Spring Cloud应用使用Ribbon或Feign来实现服务消费得到了扩展。

## 方法说明

SpringCloud与Dubbo融合使用，在项目中依旧拥有 服务接口提供方、服务接口消费方。

**为了确保契约的一致性，官方推荐将其服务接口提供方的 基础Service接口类放置在单独的Api模块中，服务接口提供方利用其Api模块的artifact依赖来进行实现。**

## 操作方法

### 构建服务接口模块

创建一个Api服务接口模块，用作存储Api服务接口类。为了测试，在其模块中先创建一个简单的抽象接口：

```java
public interface HelloService {
    String hello(String name);
}
```

### 构建服务接口提供方

创建一个服务接口提供方模块,添加相关依赖包信息:

```xml
<dependencies>
    <!-- 第一步中构建的API模块 -->
    <dependency>
        <groupId>com.didispace</groupId>
        <artifactId>alibaba-dubbo-api</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>        
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-dubbo</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    //...
</dependencies>
```

实现其对应服务接口:

```java
@DubboService
public class HelloServiceImpl implements HelloService {
    @Override
    public String hello(String name) {
        return "hello " + name;
    }
}
```

其中`@DubboService`是Dubbo中的服务注释类,效果基本和`@Service`相同。注意的是老版本Dubbo中使用的是`@Service`,而新版本为了避免重名,更改为了`@DubboService`。

随后创建启动类:

```java
@EnableDiscoveryClient
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class,args);
    }
}
```

最后设置配置文件内容：

```xml-dtd
spring:
  application:
    name: dubbo-service-provider
  cloud:
    nacos:
      discovery:
		# 服务注册中心nacos地址
        server-addr: 192.168.0.190:8848
  main:
    allow-bean-definition-overriding: true
server:
  port: 8086

dubbo:
  scan:
	# 服务类所在包名
    base-packages: com.ali.dubbo.provider.service.impl
  protocol:
    name: dubbo
    port: -1
  registry:
	# 服务注册中心地址
    address: spring-cloud://localhost
```

其中相关配置:

- `dubbo.scan.base-packages`: 指定 Dubbo 服务实现类的扫描基准包,**指向的是服务类的实现类包名，而不是服务接口类包名。**
- `dubbo.protocol`: Dubbo 服务暴露的协议配置，其中子属性 name 为协议名称，port 为协议端口（ -1 表示自增端口，从 20880 开始）
- `dubbo.registry`: Dubbo 服务注册中心配置，其中子属性 address 的值 “spring-cloud://localhost”，说明挂载到 Spring Cloud 注册中心

**注意：如果使用Spring Boot 2.1及更高版本时候，需要增加配置`spring.main.allow-bean-definition-overriding=true`，表示接受Bean的覆盖**

### 构建服务接口消费方

创建一个服务接口消费方模块,添加相关依赖包信息（基本跟提供方一样）:

```java
<dependencies>
    <!-- 第一步中构建的API模块 -->
    <dependency>
        <groupId>com.didispace</groupId>
        <artifactId>alibaba-dubbo-api</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>
    
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>        
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-dubbo</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    //...
</dependencies>
```

这里依旧需要引入其服务接口模块。

配置相关配置信息：

```xml-dtd
spring:
  application:
    name: dubbo-service-consumer
  cloud:
    nacos:
      discovery:
        server-addr: 192.168.0.190:8848
  main:
    allow-bean-definition-overriding: true
    
server:
  port: 8100
  
dubbo:
  protocol:
    name: dubbo
    port: -1
  registry:
    address: spring-cloud://localhost
  cloud:
    subscribed-services: dubbo-service-provider
```

其中这里增加了`dubbo.cloud.subscribed-services`参数，表示要订阅服务的服务名，这里配置的`alibaba-dubbo-server`对应的就是上面的服务提供方的`spring.application.name`的值，也就是服务提供方的应用名。

**注意：如果使用Spring Boot 2.1及更高版本时候，需要增加配置`spring.main.allow-bean-definition-overriding=true`，表示接受Bean的覆盖**

最后创建启动类,并编写服务控制类:

```java
@RestController
public class TestController {
    @DubboReference
    private HelloService helloService;

    @GetMapping("/test")
    public String test() {
        return helloService.hello("zssaer.cn");
    }
}
```

其中`@DubboReference`在老版本dubbo为`@Reference`，它的作用是通过dubbo服务远程实现注入，效果和常规的`@autowire`基本相同。

### 测试

最后分别启动服务提供方和服务消费方，访问对于的接口，即服务消费方成功调用了提供方的实现类。



## 总结

在Spring Cloud Alibaba的整合之下，Dubbo用户既可以享受到原本RPC带来性能优势，又可以更好的享受Spring Cloud的各种福利；而对于Spring Cloud用户来说，在服务治理层面，又对了一个不错的可选项。可以说这块的整合是真正的让这两大用户群体得到了很好的融合，起到了互相成就的作用。