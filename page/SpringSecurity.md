# Spring-Security

Java 领域里有两个权鉴框架比较出名 Shiro 和 Spring Security。

其中Shiro是一个简洁易用的开源框架，它的使用简单，功能围绕着用户认证、用户授权、身份管理几个点中，一般中小型业务现在都有用到它。

而Spring Security则是一个Spring官方的权限管理、权限授权的开源框架，它拥有比Shiro更强大的身份控制系统，而且可以在使用无状态认证（Shiro则是使用Session），所以它在大型分布式、微服务项目中更加广泛使用。

但是由于其功能框架众多，学习它并不是一个容易的事。

## 执行图

Spring Security在进行权限控制操作时，其流程大概如下：

![](../picture/d359fe34bc7860c11a1b6e50bfd0e086.jpg)

简单文字说明下:

- 客户端发起一个请求，进入 Security 过滤器链。

- 当到 LogoutFilter 的时候判断是否是登出路径，如果是登出路径则到 logoutHandler ，如果登出成功则到 logoutSuccessHandler 登出成功处理，如果登出失败则由 ExceptionTranslationFilter ；如果不是登出路径则直接进入下一个过滤器。

- 当到 UsernamePasswordAuthenticationFilter 的时候判断是否为登录路径，如果是，则进入该过滤器进行登录操作，如果登录失败则到 AuthenticationFailureHandler 登录失败处理器处理，如果登录成功则到 AuthenticationSuccessHandler 登录成功处理器处理，如果不是登录请求则不进入该过滤器。

- 当到 FilterSecurityInterceptor 的时候会拿到 uri ，根据 uri 去找对应的鉴权管理器，鉴权管理器做鉴权工作，鉴权成功则到 Controller 层否则到 AccessDeniedHandler 鉴权失败处理器处理。



## 教程

在项目中导入SpringSecurity依赖

```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

导入依赖后，运行其项目后，会运行项目地址会发现自动跳转到了其login页面下：

![](../picture/t_201208012223image-20201130140633581.png)

这个页面是SpringSecurity默认的身份认证页面。

spring security 默认的用户名是user，在新版本spring security启动的时候会生成随机密码（在启动日志中可以看到）。

身份登录成功后，那么就会正常的访问项目的其他内容。

我们可以在其application.yaml配置文件中进行修改默认密码：

```xml-dtd
spring:
  security:
    user:
      name: root
      password: root
```



### 数据库实现登录

在上述流程图中的所有流程均通过其WebSecurityConfigurerAdapter这个接口来进行，所以需要进行重写该接口来进行配置相关项目的SpringSecurity配置。

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {
...
}
```

我们重写一些方法，如下：

```java
@Configuration
public class MySecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth
                .inMemoryAuthentication()
                .withUser("admin") // 添加用户admin
                .password("{noop}admin")  // 不设置密码加密
                .roles("ADMIN", "USER")// 添加角色为admin，user
                .and()
                .withUser("user") // 添加用户user
                .password("{noop}user") 
                .roles("USER")
            	.and()
            	.withUser("tmp") // 添加用户tmp
                .password("{noop}tmp")
            	.roles(); // 没有角色
    }
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers("/product/**").hasRole("USER") //添加/product/** 下的所有请求只能由user角色才能访问
                .antMatchers("/admin/**").hasRole("ADMIN") //添加/admin/** 下的所有请求只能由admin角色才能访问
                .anyRequest().authenticated() // 没有定义的请求，所有的角色都可以访问（tmp也可以）。
                .and()
                .formLogin().and()
                .httpBasic();
    }
}
```

我们来详细说明下重写的各个常用的方法：

> configure(AuthenticationManagerBuilder auth) ：身份认证的配置
>
> configure(HttpSecurity http)：网页权限配置

当然这样操作自然是不常用的。因为一般用户登录信息都会存入数据库，而不是直接写在配置里面。。。

我们来实现数据库读取用户,修改配置文件：

```java
@Configuration
public class MySecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Autowired
    private UserDetailsService userDetailsService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(userDetailsService)// 设置自定义的userDetailsService
                .passwordEncoder(passwordEncoder());  //密码的算法
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeRequests()
                .antMatchers("/product/**").hasRole("USER")
                .antMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated() //
                .and()
                .formLogin()
                .and()
                .httpBasic()
                .and().logout().logoutUrl("/logout");
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return NoOpPasswordEncoder.getInstance();// 使用不使用加密算法保持密码
//        return new BCryptPasswordEncoder();
    }
}
```

其中我们重写 UserDetailsService ,来实现登录验证操作:

```java
@Component("userDetailsService")
public class CustomUserDetailsService implements UserDetailsService {
    @Resource
    private SimpleUserMapper simpleUserMapper;

    @Override
    public UserDetails loadUserByUsername(String login) throws UsernameNotFoundException {
        Example example = new Example(SimpleUser.class);
        example.createCriteria().andEqualTo("userName", login);
        // 1. 查询用户
        List<SimpleUser> userFromDatabase = simpleUserMapper.selectByExample(example);
        if (userFromDatabase.isEmpty()) {
            System.out.println("User  was not found in db");
            throw new UsernameNotFoundException("User " + login + " was not found in db");
            //这里找不到必须抛异常
        }

        // 2. 设置角色
        Collection<GrantedAuthority> grantedAuthorities = new ArrayList<>();
        GrantedAuthority grantedAuthority = new SimpleGrantedAuthority(userFromDatabase.get(0).getRole());
        grantedAuthorities.add(grantedAuthority);

        return new User(login, userFromDatabase.get(0).getUserPassword(), grantedAuthorities);
    }
}
```

数据库连接相关配置完后,我们可以启动服务,可以发现当login进行登录时将会执行其`UserDetailsService`中loadUserByUsername方法,进行查询用户以及其权限,然后再通过`passwordEncoder`中设置的算法进行验证密码正确性。

### 获取登录信息

对于已经登录的用户,我们可以使用SecurityContext来获取其信息

```java
@RequestMapping("/info")
public String info(){
    String userDetails = null;
    Object principal = SecurityContext.getContext().getAuthentication().getPrincipal();
    if(principal instanceof UserDetails) {
        userDetails = ((UserDetails)principal).getUsername();
    }else {
        userDetails = principal.toString();
    }
    return userDetails;
}
```

`SecurityContext`是安全的上下文，所有的登录的用户信息数据都是保存到SecurityContext中,它主要通过SecurityContextHolder来获取。

`SecurityContext context = SecurityContextHolder.getContext();`

SecurityContext其中拥有多个用户信息:

> Authentication表示当前的认证情况，可以获取的对象有：
>
> UserDetails：获取用户信息，是否锁定等额外信息。
>
> Credentials：获取密码。
>
> isAuthenticated：获取是否已经认证过。
>
> Principal：获取用户，如果没有认证，那么就是用户名，如果认证了，返回UserDetails。

### 无状态实现

spring security会在默认的情况下将认证信息放到HttpSession中。

但是对于我们的前后端分离的情况，如app，小程序，web前后分离等，httpSession就没有用武之地了。这时我们可以通过`configure(httpSecurity)`设置spring security是否使用httpSession。

我们需要修改配置:

```java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .sessionManagement()
            	//设置无状态，所有的值如下所示。
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                // code...
    }
```

共有四种值，其中默认的是ifRequired。

- ***always*** – a session will always be created if one doesn’t already exist，没有session就创建。
- ***ifRequired*** – a session will be created only if required (**default**)，如果需要就创建（默认）。
- ***never*** – the framework will never create a session itself but it will use one if it already exists
- ***stateless*** – no session will be created or used by Spring Security 不创建不使用session

由于前后端不通过保存session和cookie来进行判断，所以为了保证spring security能够记录登录状态，所以需要传递一个值，让这个值能够自我验证来源，同时能够得到数据信息。选型我们选择[JWT](https://www.cnblogs.com/dl610455894/p/JWT.md)。对于java客户端我们选择使用[jjwt](https://github.com/jwtk/jjwt)。

对此添加JJWT依赖:

```xml
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

#### 创建工具类JWTProvider

```java
package com.example.springsecurity.component;

import com.example.springsecurity.mapper.SimpleUserMapper;
import com.example.springsecurity.model.SimpleUser;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import io.jsonwebtoken.io.Decoders;
import io.jsonwebtoken.security.Keys;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;
import tk.mybatis.mapper.entity.Example;

import javax.annotation.PostConstruct;
import java.nio.charset.StandardCharsets;
import java.security.Key;
import java.util.*;

/**
 * @description: TODO
 * @author: Zhaotianyi
 * @time: 2021/11/17 11:22
 */
@Component
public class JWTProvider {
    private Key key;   // 私钥
    private long tokenValidityInMilliseconds; // 有效时间
    private long tokenValidityInMillisecondsForRememberMe; // 记住我有效时间

    @Autowired
    private SimpleUserMapper simpleUserMapper;
    @Autowired
    private JJWTProperties jjwtProperties; // jwt配置参数

    @PostConstruct
    public void init() {
        byte[] keyBytes;
        String secret = jjwtProperties.getSecret();
        if (StringUtils.hasText(secret)) {
            System.out.println("Warning: the JWT key used is not Base64-encoded. " +
                    "We recommend using the `jhipster.security.authentication.jwt.base64-secret` key for optimum security.");
            keyBytes = secret.getBytes(StandardCharsets.UTF_8);
        } else {
            System.out.println("Using a Base64-encoded JWT secret key");
            keyBytes = Decoders.BASE64.decode(jjwtProperties.getBase64Secret());
        }
        this.key = Keys.hmacShaKeyFor(keyBytes); // 使用mac-sha算法的密钥
        this.tokenValidityInMilliseconds =
                1000 * jjwtProperties.getTokenValidityInSeconds();
        this.tokenValidityInMillisecondsForRememberMe =
                1000 * jjwtProperties.getTokenValidityInSecondsForRememberMe();
    }

    public String createToken(UserDetails userDetails, boolean rememberMe) {
        long now = (new Date()).getTime();
        Date validity;
        if (rememberMe) {
            validity = new Date(now + this.tokenValidityInMillisecondsForRememberMe);
        } else {
            validity = new Date(now + this.tokenValidityInMilliseconds);
        }

        Map<String ,Object> map = new HashMap<>();
        map.put("sub",userDetails.getUsername());
        return Jwts.builder()
                .setClaims(map) // 添加body
                .signWith(key, SignatureAlgorithm.HS512) // 指定摘要算法
                .setExpiration(validity) // 设置有效时间
                .compact();
    }


    public String createToken(Authentication authentication, boolean rememberMe) {
        long now = (new Date()).getTime();
        Date validity;
        if (rememberMe) {
            validity = new Date(now + this.tokenValidityInMillisecondsForRememberMe);
        } else {
            validity = new Date(now + this.tokenValidityInMilliseconds);
        }
        Example example = new Example(SimpleUser.class);
        example.createCriteria().andEqualTo("userName",authentication.getName());
        List<SimpleUser> list = simpleUserMapper.selectByExample(example);
        Map<String ,Object> map = new HashMap<>();
        map.put("sub",authentication.getName());
        map.put("user",list.get(0));
        return Jwts.builder()
                .setClaims(map) // 添加body
                .signWith(key, SignatureAlgorithm.HS512) // 指定摘要算法
                .setExpiration(validity) // 设置有效时间
                .compact();
    }

    public Authentication getAuthentication(String token) {
        Claims claims = Jwts.parserBuilder()
                .setSigningKey(key)
                .build()
                .parseClaimsJws(token).getBody(); // 根据token获取body
        SimpleUser principal;
        Collection<? extends GrantedAuthority> authorities;

        Example example = new Example(SimpleUser.class);
        example.createCriteria().andEqualTo("userName",claims.getSubject());
        principal = simpleUserMapper.selectByExample(example).get(0);
        authorities = principal.getAuthorities();
        return new UsernamePasswordAuthenticationToken(principal, token, authorities);
    }
}
```

