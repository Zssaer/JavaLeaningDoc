# JWT

JWT全程为JSON Web Token，顾名思义就是以Json形式的存在的Web密令。

由于是一串Json文本，所以它十分便捷，规范也非常轻巧。这个规范允许我们使用JWT在用户和服务器之间传递安全可靠的信息。

## 用处

大伙都知道在Web中，拥有Session和Cookie、Loacl Storage等存储方式。但是他们都有一个非常致命的缺点：自身不能自动实现保密。

Session的机制导致或许可以进行达到一些安全性的范畴，Cookie则完全暴露，经常被黑客滥用，导致一些浏览器在一些网站上默认关闭Cookie功能，而Loacl Storage不仅没有安全性，还会对造成无用数据堆积。

JWT本身由各个算法而来，而它又集成了Cookie过期时间的功能，所以被广泛使用于传输保密内容中。

近年来，前后端分离的流行，自然，前后端的通讯是需要解决的问题之一，特别是在身份认证方面。

传统的JAVA身份认证框架，如Spring Security、Shiro等，都是采用的Session进行身份认证的，通过内部的缓存存储服务器存储Session。所以前后端Session认证则十分麻烦，使用JWT实现无状态的认证则是主流。

JWT身份认证就像 人的身份证一样，只要携带就代表是本人一样。

## 基本结构

```
eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ1c2VyIiwiZXhwIjoxNjM3Mjg5MTY0fQ.yl-5VYvv2yjD906rWvf6GyACjZLeQLHaG-ACwIBFs3FUkbZelbNbM0qy71AlJIbfMiLlxSkvhSGenSZz0R5xww
```

上述就是一个标准的JWT。

JWT实际上就是一个字符串，它由三部分组成：头部（header）、载荷（payload）与签名（signature）。

![](../picture/aHR0cHM6Ly9pLmxvbGkubmV0LzIwMTkvMTAvMTUvRXFZTm9iN1ZGOHRTZUdqLnBuZw.png)

如上图，三个部分使用“.”来进行连接。

### 头部（header）

头部一般由 两个部分组成：token的类型 和 使用的算法。

形式如下：

```json
{
    "typ":"JWT",
	"alg":"HS256",
    ...
}
```

除此之外,还可以有一些如zip（指示压缩方法）等自定义的字段。

### 载荷（payload）

payload部分是JWT存储信息的部分，包含着Claims（声明），其实就是存储的的数据。

一般声明分为以下三种类型：

> * Registered claims：预定义的声明，如：
>   * iss：issuer 发布者的URL地址
>   * sub：subject JWT面向的用户，不常用
>   * aud：audience 接受者的URL地址
>   * exp：expiration JWT失效的时间（Unix timestamp）
>   * nbf：not before 该时间前JWT无效（Unix timestamp）
>   * iat：issued at JWT发布时间（Unix timestamp）
>   * jti：JWT ID

当然上面的这些属性也不是必须都存在，可能存在一些其他信息，这些信息可以被提取，解析后可以起到传输数据作用。

```
{
    "sub":"456789123",
    "name":"Jack Ma",
    "admin":false
}
```

### 签名（signature）

有些文章将其译作"签证"。它的作用就是验证其是头部、载荷 是否被他人修改（JWT毕竟是Json文本，能被修改，从而导致不法分子伪冒操作）。

它的原理：

**将其头部、载荷两个部分内容进行Base64编码，随后加上秘匙，最后在依据头部中声明的加密方式进行加盐秘匙组合加密。**

比如：

```java
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

**这儿这个秘匙（secret） 就像是一把钥匙，生成 签名 就像是 用这把钥匙制作锁，而后面会谈到的JWT解密就像是用这把钥匙来开锁。**





## 在JAVA中使用

Java中目前最流行的JWT库就是[JJWT](https://github.com/jwtk/jjwt)。

使用JJWT需要导入3个依赖包:

```java
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.2</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId> <!-- or jjwt-gson if Gson is preferred -->
    <version>0.11.2</version>
    <scope>runtime</scope>
</dependency>
```

当然如果你使用的是JDK10或者以下的老JDK版本的话,而且还需要使用 诸如RSASSA-PSS (PS256, PS384, PS512)算法的话,就需要在额外添加一个依赖包(JDK10以上的不需要):

```java
<dependency>
    <groupId>org.bouncycastle</groupId>
    <artifactId>bcprov-jdk15on</artifactId>
    <version>1.60</version>
    <scope>runtime</scope>
</dependency>
```



### 使用JJWT创建一个JSON

创建一个Json大概分为3个部分:

1. *创建*一个含有默认载荷的JWT
2. 用你的秘钥 *签名*（加密）这个JWT（秘钥必须满足HMAC-SHA-256算法）
3. *打包*JWT，生成为一个字符串

```java
JwtBuilder builder= Jwts.builder()
 .setId("1")   //设置唯一编号
 .setSubject("ZSSAER")  //设置主题  可以是JSON数据
 .setIssuedAt(new Date())  //设置签发日期
 .signWith(SignatureAlgorithm.HS256,"XXXXXXX");//设置签名 使用HS256算法，并设置SecretKey(字符串)
//构建 并返回一个字符串 
System.out.println( builder.compact() );
```

其中“XXXXXXX"为你设置的密匙，接下来你需要使用它来进行解密。

一般来说，为了安全起见，JWT对其密匙是有相应的要求的，比如SHA-256加密的话，就需要256位的密匙，所以需要提前将其密匙进行SHA-256加密，这个操作可以使用JAVA或者去在线的SHA-256生成工具直接生成这个密匙。



前面说了，JWT中的荷载 中claims，除了想上面那样，一个一个set添加进去之外，还可以直接打包，它接受一个Map<String,?>的对象。

```java
Map<String, Object> map = new HashMap<>(16);
map.put("sub", userDetails.getUsername());
Jwts.builder()
        // 添加claims
        .setClaims(map)
    	...
```

对于其map中的第一个String索引，我们可以前往Claims接口看：

```java
public interface Claims extends Map<String, Object>, ClaimsMutator<Claims> {

    /** JWT {@code Issuer} claims parameter name: <code>"iss"</code> */
    public static final String ISSUER = "iss";

    /** JWT {@code Subject} claims parameter name: <code>"sub"</code> */
    public static final String SUBJECT = "sub";

    /** JWT {@code Audience} claims parameter name: <code>"aud"</code> */
    public static final String AUDIENCE = "aud";

    /** JWT {@code Expiration} claims parameter name: <code>"exp"</code> */
    public static final String EXPIRATION = "exp";

    /** JWT {@code Not Before} claims parameter name: <code>"nbf"</code> */
    public static final String NOT_BEFORE = "nbf";

    /** JWT {@code Issued At} claims parameter name: <code>"iat"</code> */
    public static final String ISSUED_AT = "iat";

    /** JWT {@code JWT ID} claims parameter name: <code>"jti"</code> */
    public static final String ID = "jti";
```

这些名称都在上面载荷介绍中说明了，只需要将其命名为对应的名称即可。



### 使用JJWT解密JWT

有加密必然有解密。对于JWT解密而言，我们一般只在乎JWT中的荷载内容，就像我们收快递没人在乎快递包装，而是关心里面的产品，当然盒子收集控除外。

和登录操作一样，既然解密,就需要加密时的密匙，不然解密就不会成功。

```java
// 根据token获取body
Claims claims;
claims = Jwts.parserBuilder()
        // 设置密匙
        .setSigningKey(key)
        .build()
        // 解密的JWT
        .parseClaimsJws(token).getBody();
```

通过getBody()方法，我们可以直接获取到荷载中的Claims，然后通过Claims我们便可以使用对应的Setting方法进行提取内容了。