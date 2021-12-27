# FreeMarker

 *FreeMarker*是一款模板引擎： 即一种基于模板和要改变的数据， 并用来生成输出文本（HTML网页、电子邮件、配置文件、源代码等）的通用工具。 

FreeMarker最初的设计出来是用作Web开发框架中的动态Html页面的，它也可以用作非Web应用环境下。它的同类有大名鼎鼎的JSP、Velocity。

在当时前后端还未成型的Web开发行业中，JavaWeb页面被直接集中在了Java项目中，后端开发为了简便开发使用，JSP往往是那时候的标配。而在现在前后端开发时代，JSP、Velocity早已经鲜有使用了，甚至如今一些后端程序员根本不需要了解前端知识，JSP这类的动态Html渐渐没人使用了。

但FreeMarker不一样，它如今因为其动态生成数据的能力被广泛应用在了业务生成和开源项目中。

## 使用

### POM依赖

```xml
<!-- freemarker-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-freemarker</artifactId>
	<version>2.4.4</version>
    <exclusions><!-- 去掉SpringBoot默认的logback配置 -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

### 关于FTL文件

FTl是FreeMarker模板格式。它是一种Html格式，但是它不同Html，它支持在内容中使用动态语句。

```java
package ${classPath};

public class ${className} {

    public static void main(String[] args) {
        System.out.println("${helloWorld}");
    }

}
```

其中 ${xxx} ，xxx 相当于变量占位符，java后台给xxx赋值后，再通过${}输出。

注释写法： <#-- XXXXX -->

上述ftl文件是一个生成Java类的模板，其中classPath、className、helloWorld均由后台传入。

### FTL中的动态语句

和JSP的JSTL一样，FreeMarker也有自己的动态语句。

判断语句:

```html
<#if animals.python.price != 0>
  System.out.println("${helloWorld}");
</#if>　
```

当#if中的条件语句为真时，FreeMarker生成其中的语句

Map遍历：

```html
<#-- 通过Key遍历Map： -->
<#list mapData?keys as key>
Key: ${key} - Value: ${mapData[key]}
</#list>
    
<#-- 通过Value遍历Map： -->
<#list mapData?values as value>
Value: ${value}
</#list>
```

其中mapData为后台传入的Map集合。

List遍历：

```html
<#list listData as value>${value} </#list>
```

其中listData为后台传入的List集合。



### FreeMarker配置

我们在利用FreeMarker进行生成代码前我们还得先配置好FreeMarker，以便后续操作正常。

```java
// 获取FreeMarker配置类
Configuration config = new Configuration(freemarker.template.Configuration.VERSION_2_3_23);
```

这里需要注意的是，这个Configuration类是其`freemarker.template`包下的，而不是Spring包下的。

目前新版本的FreeMarker下的Configuration空参构造方法已经过时了，新构造函数需要获取一个版本实例，我这里这个依赖版本下使用2.3.23，使用使用的是`freemarker.template.Configuration.VERSION_2_3_23`。

#### 绑定模板文件夹目录

随后在配置类中设置FreeMarker模板文件夹。

```java
// 设置FreeMarker模板文件夹目录
config.setDirectoryForTemplateLoading(new File(System.getProperty("user.dir")+"/admin-provider/src/test/resources/template"));
```

这里的`System.getProperty("user.dir")`是项目在硬盘上的基础路径。

#### 设置默认字节编码

为了防止模板解析中出现字节乱码，设置默认的字节编码。

```java
// 设置模板解析的字节编码
conf.setDefaultEncoding("UTF-8");
```

#### 设置模板异常处理器

当模板处理时出现异常时，可以通过设置模板异常处理器来进行后续解决。

```java
// 设置模板异常处理,直接跳过异常区域
conf.setTemplateExceptionHandler(TemplateExceptionHandler.IGNORE_HANDLER);
```

这儿有3个常用的 内置异常处理器：

> IGNORE_HANDLER：忽略错误
>
> DEBUG_HANDLER：将堆栈跟踪信息输出到客户端，然后重新抛出异常。
>
> RETHROW_HANDLER：简单地重新抛出异常； 这应该在大多数生产系统中使用。



### 生成代码

配置完FreeMarker配置后，我们就可以根据向模板中注入数据，然后生成代码了。

```java
private static final String CLASS_NAME = "User";

Map<String, Object> data = new HashMap<>();
data.put("classPath", "com.zssaer.test");
data.put("className", CLASS_NAME);
data.put("helloWorld", "This is Wrod by FreeMarker Generated");
```

FreeMarker模板中所需要得参数需要由Map<String,Object>来进行包装。

随后我们使用 FreeMarker配置类的getTemplate方法进行获取对应模板，然后再由它的process方法进行处理渲染。

```java
// 生成类的文件
File file = new File(PROJECT_PATH + JAVA_PATH + PACKAGE_PATH_SERVICE + CLASS_NAME + ".java");
// 生成类的目录不存在则 创建目录
if (!file.getParentFile().exists()) {
                file.getParentFile().mkdirs();
}
config.getTemplate("service.ftl").process(data,
        new FileWriter(file));
```

其中模板的process方法就是生成代码的操作，它接受两个参数：

> Object dataModel：模板中的携带参数，通常为Map<String,Object>。
>
> Writer out：一个文件打印输出流，这里我们可以用new FileWriter(文件对象)。

这里在生成代码之前还是要注意下是否存在该文件夹，因为FreeMarker模板生成是不会连带生成文件夹的，那样找不到文件夹生成操作就会抛出错误。



就这样我们就生成了对应ftl模板的Java类了，FreeMarker在一些自动化项目中被广泛使用，可以被用作很多业务场景，使用它大大减少了代码量。

