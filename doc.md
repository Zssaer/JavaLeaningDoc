# 获取验证码 easy-captcha

```xml
<!-- 验证码easy-captcha -->
<dependency>
   <groupId>com.github.whvcse</groupId>
   <artifactId>easy-captcha</artifactId>
   <version>${easy.captcha.version}</version>
</dependency>
```

```java
// 算数类型验证码
ArithmeticCaptcha captcha = new ArithmeticCaptcha(130, 48);
// 中文类型
ChineseCaptcha captcha = new ChineseCaptcha(130, 48);
```

```java
// 几位数运算，默认为两位
captcha.setLen(2);
// 获取运算的公式：3+2=?
captcha.getArithmeticString();
// 获取运算的结果：5
String value = captcha.text();
```

```java
String key = UuidUtil.createUuid();
// 存入redis并设置过期时间为5分钟
RedisUtil.set(key, value, 600);
HashMap<String, String> captchaMap = new HashMap<String, String>(2);
captchaMap.put("captchaKey", key);
captchaMap.put("image", captcha.toBase64());
// 将key和验证码base64返回给前端
return Result.success(captchaMap);
```

------



# TK.mybatis框架使用

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.0.3-beta1</version>
</dependency>

<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper</artifactId>
    <version>4.0.0</version>
</dependency>
```

创建BaseMapper接口继承Mapper<T>, ConditionMapper<T> 

```java
public interface BaseMapper<T> extends Mapper<T>, ConditionMapper<T> {
    ...
}
```

创建对应Dao层接口,例如<SysUser>类的Dao

```Java
public interface SysUserMapper extends BaseMapper<SysUser> {
    ....
}
```

### BaseMapper<T>方法：

```java
/**
 * 保存一个实体，null属性也会保存
 * 
 * @param record
 * @return
 */
int insert(T record);

/**
 * 保存一个实体，null属性不会保存
 * 
 * @param record
 * @return
 */
int insertSelective(T record);
 
/**
 * 根据实体属性作为条件进行删除，查询条件使用等号
 */
int delete(T record);
 
/**
 * 根据主键更新属性不为null的值
 */
int updateByPrimaryKeySelective(T record);
 
/**
 * 根据实体中的属性值进行查询，查询条件使用等号
 */
List<T> select(T record);
 
/**
 * 查询全部结果，select(null)方法能达到同样的效果
 */
List<T> selectAll();
 
/**
 * 根据实体中的属性进行查询，只能有一个返回值，有多个结果是抛出异常，查询条件使用等号
 */
T selectOne(T record);
 
/**
 * 根据实体中的属性查询总数，查询条件使用等号
 */
int selectCount(T record);
```
### IdsMapper<T>方法：

```java
/**
 * 根据主键@Id进行查询，多个Id以逗号,分割
 * @param id
 * @return
 */
List<T> selectByIds(String ids);

/**
 * 根据主键@Id进行删除，多个Id以逗号,分割
 * @param id
 * @return
 */
int deleteByIds(String ids);
```
### ConditionMapper<T>方法：

 /**
	 * 根据Condition条件进行查询
	 */
	public List<T> selectByCondition(Object condition);

```java
/**
 * 根据Condition条件进行查询
 */
public int selectCountByCondition(Object condition);
 
/**
 * 根据Condition条件删除数据，返回删除的条数
 */
public int deleteByCondition(Object condition);
 
/**
 * 根据Condition条件更新实体`record`包含的全部属性，null值会被更新，返回更新的条数
 */
public int updateByCondition(T record, Object condition);
 
/**
 * 根据Condition条件更新实体`record`包含的全部属性，null值会被更新，返回更新的条数
 */
public int updateByConditionSelective(T record, Object condition);
```
其中传入的Object condition应为tk.mybatis.mapper.entity.Condition

------

# JWT（JSON Web Token)跨域身份验证

*结构： heard.payload.singnature*

​		    *标头.有效负载.签名*

## 结构解析

1.标头（heard）：**包含令牌的类型以及使用的签名算法。**

2.有效负载（payload）：**包含实体和其他数据的声明。**尽管它可以防止被篡改，但任何人依然可以读取这些签名的信息。 除非加密，否则不要在 JWT 的有效载荷或头部元素中放入秘密信息。

3.签名（singnature）：**签名用于验证消息是否在整个过程中被更改。**对于使用私钥签名的令牌，它还可以验证 JWT 的发送方是否是它所说的那个发送方。

## 使用JWT

#### 0.Maven依赖

```xml
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.11.0</version>
</dependency>
```

#### 1.利用数据，生成对应Token

```java
Calendar instance = Calendar.getInstance();
        instance.add(Calendar.SECOND,20);
        
String token = JWT.create()
        .withHeader(map)  //设置header
        .withClaim("userId", 21)  //设置payload
        .withClaim("userName", "xxx")
        .withExpiresAt(instance.getTime())  //指定令牌过期时间
        .sign(Algorithm.HMAC256("@QWER@"));//设置singnature 其中使用HMAC256算法
```

#### 2.使用对应Token，获取数据

```java
JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256("@QWER@")).build();  //获取对应Jwt算法解析器

//解析对应Token，获得解码后的Jwt
DecodedJWT decodedJWT = jwtVerifier.verify("eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9" +
        ".eyJ1c2VyTmFtZSI6Inh4eCIsImV4cCI6MTYwMzc2NTU4MCwidXNlcklkIjoyMX0" +
        ".cHJX4xoGPFBI8Qgk6me_44F1y_lunIDS9V0DJfLudw8");
        
//获取对应Token中数据
System.out.println(decodedJWT.getClaim("userId").asInt());
System.out.println(decodedJWT.getClaim("userName").asString());
```

------

# SpringBoot+Mybatis整合

## Maven依赖

```xml
		<!--mysql数据库驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.0</version>
        </dependency>
```

## 操作方法

#### 1.编写实体类

```java
@Table(name = "SYS_ORDER")   //声明此对象映射到数据库的数据表
public class SysOrder implements Serializable {
   private static final long serialVersionUID = 2688088497753868718L;
    
   @Id   //主键字段
   private String merchantOrderNo;
   private String merchantNo;
   private Integer amount;
   ...
       
   public String getMerchantOrderNo() {
		return merchantOrderNo;
	}

	public void setMerchantOrderNo(String merchantOrderNo) {
		this.merchantOrderNo = merchantOrderNo;
	}

	public String getMerchantNo() {
		return merchantNo;
	}

	public void setMerchantNo(String merchantNo) {
		this.merchantNo = merchantNo;
	}
	...
}
```

#### 2.编写mapper接口

```java
@Mapper  //指定这是一个操作数据库的mapper
public interface SysOrderMapper extends BaseMapper<SysOrder> {

   BizDataDTO getBizDataByOrderNo(@Param("merchantOrderNo") String merchantOrderNo);

   List<SysOrderDTO> queryOrderData(@Param("orderQueryRequest") OrderQueryRequest orderQueryRequest);

   SysOrderDTO getOrderByOrderNo(@Param("merchantOrderNo") String merchantOrderNo);
   ...
}
```

#### 3.编写mapper xml映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="cn.com.bosssoftcq.ipp.mapper.SysOrderMapper">
  <resultMap id="BaseResultMap" type="cn.com.bosssoftcq.ipp.model.doo.SysOrder">
    <id column="MERCHANT_ORDER_NO" jdbcType="VARCHAR" property="merchantOrderNo" />
    <result column="MERCHANT_NO" jdbcType="VARCHAR" property="merchantNo" />
    <result column="AMOUNT" jdbcType="DECIMAL" property="amount" />
  </resultMap>
  ...
    <select id="getOrderByOrderNo"
            parameterType="java.lang.String" resultMap="orderWithBizList">
		SELECT
		so.*,bi.biz_ID,bi.sys_type
		FROM
		sys_order so,sys_order_biz bi
		where  
		so.merchant_order_no = bi.merchant_order_no
		and so.MERCHANT_ORDER_NO = #{merchantOrderNo,jdbcType=VARCHAR}
	</select>
    ...
 </mapper>
```

#### 4.编写service接口

```java
public interface SysOrderService extends BaseService<SysOrder>{
    
    PageInfoBO queryOrderData(OrderQueryRequest orderQueryRequest);
    
    Result cancelOrderByMerchantOrderNo(String merchantOrderNo);
    
    ...
}
```

#### 5.编写service实现类

```java
@Service  //需要在接口实现类中使用@Service注解，才能被SpringBoot扫描
public class SysOrderServiceImpl extends BaseServiceImpl<SysOrder> implements SysOrderService {

   private static final Logger log = LoggerFactory.getLogger(SysOrderServiceImpl.class);

   @Autowired
   private SysOrderMapper orderMapper;
   @Autowired
   private EduBizSplitDataMapper eduBizSplitDataMapper;
   ...
   @Override
	public PageInfoBO queryOrderData(OrderQueryRequest orderQueryRequest) {
		//如果是super则查询所有，否则上查下
		UserDTO user = UserUtil.getUser();
		if (user.isSuperAdmin()){
			orderQueryRequest.setDeptId(null);
		}else{
			orderQueryRequest.setDeptId(user.getDeptId());
		}
		PageHelper.startPage(orderQueryRequest.getPage(),orderQueryRequest.getLimit());
		List<SysOrderDTO> orders = orderMapper.queryOrderData(orderQueryRequest);
		if (!orders.isEmpty()) {
			PageInfo<SysOrderDTO> pageInfo = new PageInfo<>(orders);
			return new PageInfoBO(pageInfo.getTotal(), pageInfo.getList());
		}
		return null;
	}
    ...
}
```

#### 6.编写controller文件

```java
@RestController
public class OrderController {
   
    private static  final Logger LOGGER = LoggerFactory.getLogger(OrderController.class);
    @Autowired
    private SysOrderService sysOrderService;

    @GetMapping("/order/queryData")
    public Result queryOrderData(OrderQueryRequest orderQueryRequest) {
        return Result.success(sysOrderService.queryOrderData(orderQueryRequest));
    }

}
```

#### 7.配置property文件(或者yml文件)

```yaml
mybatis:
    # Mybatis配置Mapper路径
    mapper-locations: classpath:mapping/*.xml
    # Mybatis配置Model类对应
    type-aliases-package: cn.com.bosssoftcq.ipp.model
```

------

# Mybatis逆向工程

## 操作方法

### 一.Maven依赖

```xml
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.7</version>
</dependency>
```

### 二.配置generatorConfig.xml文件

```xml
<?xml version='1.0' encoding='UTF-8'?>
<!DOCTYPE generatorConfiguration
      PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
      "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
   <context id="sakila" targetRuntime="MyBatis3">
      <!-- 生成的Java文件的编码 -->
      <property name="javaFileEncoding" value="UTF-8" />
      <!-- 格式化java代码 -->
      <property name="javaFormatter"
              value="org.mybatis.generator.api.dom.DefaultJavaFormatter" />
      <!-- 格式化XML代码 -->
      <property name="xmlFormatter"
              value="org.mybatis.generator.api.dom.DefaultXmlFormatter" />

      <commentGenerator>
         <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
         <property name="suppressAllComments" value="true"  />
         <!-- 是否生成注释代时间戳 -->
         <property name="suppressDate" value="true" />
      </commentGenerator>

      <!--数据库连接的信息：驱动类、连接地址、用户名、密码 -->
      <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                  connectionURL="jdbc:mysql://localhost:3306/23?useSSL=false&amp;useUnicode=false&amp;characterEncoding=UTF8"
                  userId="root"
                  password="200428">
      </jdbcConnection>

      <javaTypeResolver>
         <!-- 把jdbc对应的日期类型转成java8中的LocateDateTime类型 -->
         <property name="useJSR310Types" value="true"/>
      </javaTypeResolver>


      <!-- targetProject:生成PO类的位置 -->
      <javaModelGenerator targetPackage="com.example.seconddemowebrestfulcrud.entity"
                     targetProject=".\src\main\java">
         <!-- 从数据库返回的值被清理前后的空格 -->
         <property name="trimStrings" value="true"/>
      </javaModelGenerator>

      <!-- targetProject:mapper映射文件生成的位置 -->
      <sqlMapGenerator targetPackage="mapper"
                   targetProject=".\src\main\resources"/>

      <!-- targetPackage：mapper接口生成的位置 -->
      <javaClientGenerator type="XMLMAPPER"
                      targetPackage="com.example.seconddemowebrestfulcrud.mapper"
                      targetProject=".\src\main\java"/>

      <!-- 指定数据库表 -->
      <table schema="23" tableName="user_info"
            domainObjectName="User"
            enableCountByExample="false"
            enableDeleteByExample="false" enableSelectByExample="false"
            enableUpdateByExample="false">
         <!-- 如果设置为true，生成的entity类会直接使用column本身的名字，而不会再使用驼峰命名方 -->
         <property name="useActualColumnNames" value="false" />
         <!-- 生成的SQL中的表名将不会包含schema和catalog前缀 -->
         <property name="ignoreQualifiersAtRuntime" value="true" />
      </table>
   </context>
</generatorConfiguration>
```

### 三.编写生成器

```java
public class Generator {

    public void generator() throws Exception {
        List<String> warnings = new ArrayList<String>();
        boolean overwrite = true;
        /**指向逆向工程配置文件*/
        File configFile = new File("D:\\first-app-demo\\second-demo-web-restfulcrud\\src\\main\\resources\\generator" +
                "\\generatorConfig.xml");
        ConfigurationParser parser = new ConfigurationParser(warnings);
        Configuration config = parser.parseConfiguration(configFile);
        DefaultShellCallback callback = new DefaultShellCallback(overwrite);
        MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config,
                callback, warnings);
        myBatisGenerator.generate(null);
    }

    public static void main(String[] args) throws Exception {
        try {
            Generator generatorSqlmap = new Generator();
            generatorSqlmap.generator();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

最后，运行其中main方法即可生成POJO实体类、Mapper接口、Xml映射文件。

------

# PageHelper 分页

## Maven依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.3.0</version>
</dependency>
```

## 操作方法

### 1.配置文件

```yaml
pagehelper:  
  # 指定分页插件使用哪种方言
  helperDialect: mysql
  # 分页合理化，默认值为false。当该参数设置为 true 时，pageNum<=0 时会查询第一页，pageNum>pages，会查询最后一页。
  reasonable: true 
  ...
```

### 2.编写对应需要分页controller层方法

```java
    /**
     * 会员管理
     */
    @RequestMapping("/manageMember")
    public String manageMember(@RequestParam(defaultValue = "1") int pageNum,
                               @RequestParam(defaultValue = "10") int pageSize,
                               Model model){
        // 设置查询分页大小
        PageHelper.startPage(pageNum,pageSize);
        // 装入PageInfo容器中
        PageInfo pageInfo=new PageInfo(adminMemberService.selectAllUser());
        model.addAttribute("pageInfo",pageInfo);
        return "adminMemberManage";
    }

   public PageInfoBO queryOrderData(OrderQueryRequest orderQueryRequest) {
		//如果是super则查询所有，否则上查下
		UserDTO user = UserUtil.getUser();
		if (user.isSuperAdmin()){
			orderQueryRequest.setDeptId(null);
		}else{
			orderQueryRequest.setDeptId(user.getDeptId());
		}
		PageHelper.startPage(orderQueryRequest.getPage(),orderQueryRequest.getLimit());
		//查询数据
		List<SysOrderDTO> orders = orderMapper.queryOrderData(orderQueryRequest);
		if (!orders.isEmpty()) {
			PageInfo<SysOrderDTO> pageInfo = new PageInfo<>(orders);
             // pageInfo.getTotal ：获取总条数 
             //pageInfo.getList ：获取数据
			return new PageInfoBO(pageInfo.getTotal(), pageInfo.getList());
		}
		return null;
	}
}
```



------

# Redis基础学习

## 服务器

### 打开服务器

```
redis-server.exe
```

### 访问服务器

```
redis-cli.exe -h 服务器IP -p 端口号(默认6379)
```



## 数据类型

Redis支持5类数据类型【String（字符串）、hash（哈希值）、list（列表）、set（集合）、zset（有序集合）】

### 1.String

string 是 redis 最基本的类型，一个 key 对应一个 value。

string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。

**string 类型的单个值最大能存储 512MB。**



#### SET命令

设置指定 key 的值

```
SET -key- -value-  
```

#### GET命令

获取指定 key 的值

```
GET -key-
```

#### GETSET命令

将给定 key 的值设为 value ，并返回 key 的旧值

```
GETSET -key- -value-
```

#### STRLEN命令

返回 key 所储存的字符串值的长度

```
STRLEN -key-
```

#### MSET命令

同时设置一个或多个 key-value 对

```
MSET -key1- -value1- -key2- -value2- ...
```



### 2.Hash

 hash 哈希 是一个键值(key=>value)对集合，string 类型的 field 和 value 的映射表。

hash 特别适合用于存储对象。

每个 hash 可以存储 232 - 1 键值对（40多亿）。



#### HMSET命令

为指定key设置hash表

```
HMSET -key- -field1- -value1- -field2- -value2- ...
```

#### HGETALL命令

遍历整个key的hash表内容

```
HGETALL -key-
```

#### HGET命令

获取某个key中指定hash表内容

```
HGET -key- -field-
```

#### HLEN命令

获取hash表中字段的数量

```
HLEN -key-
```



### 3.List

List列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。



#### LPUSH命令

将一个或多个值插入到列表头部。

```
LPUSH -key- -value1- -value2- ...
```

#### LSET命令

通过索引来设置元素的值。

```
LSET -key- -index- -value-
```

#### LLEN命令

获取列表长度。

```
LLEN -key-
```

#### LIINDEX命令

通过索引获取列表中的元素。

```
LINDEX -key- -index-
```



### 4.Set

 Set 是 string 类型的无序集合。

Set不允许内容重复。

集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 0或者1



#### SADD命令

添加一个 string 元素到 key 对应的 set 集合中，成功返回 1，如果元素已经在集合中返回 0。

```
SADD -key- -value1- -value2- ...
```

#### SMEMBER命令

返回集合中的所有的成员。 

```
SMEMBER -key-
```

#### SCARD命令

命令返回集合中元素的数量。

```
SCARD -key-
```

#### SISMEMBER命令

判断成员元素是否是集合的成员。

```
SISMEMBER -key- -value-
```



### 5.Zset

有序集合和集合一样也是 string 类型元素的集合,且不允许重复的成员。

不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。

**有序集合的成员是唯一的,但分数(score)却可以重复。**

分数值可以是整数值或双精度浮点数。



#### ZADD命令

向有序集合添加一个或多个成员，或者更新已存在成员的分数

```
ZADD -key- -score1- -value1- -score2- -value2- ...
```

#### ZSCARD命令

计算集合中元素的数量。

```
ZSCARD -key-
```

#### ZRANGE命令

通过索引区间返回有序集合指定区间内的成员

```
ZRANGE -key- -minScore- -maxScore-
```



## **发布订阅**

 发布订阅 (pub/sub) 是一种消息通信模式：发送者 (pub) 发送消息，订阅者 (sub) 接收消息。

<img src="picture\pubsub1.png"  />

当有新消息通过 PUBLISH 命令发送给频道 channel1 时， 这个消息就会被发送给订阅它的三个客户端：

![](picture\pubsub2.png)



#### SUBSCRIBE命令

订阅给定的一个或多个频道的信息。

```
Subscribe  -channel1- -channel2- ....
```

#### PUBLISH命令

将信息推送到指定的频道。

```
Publish -channel- -message-
```

#### UNSUBSCRIBE命令

退订指定频道。

```
Unsubscribe -channel1- -channel2-
```



## SpringBoot+Redis

### Maven依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.3.0.RELEASE</version>
</dependency>
```

### 配置方法

#### 1.配置properties文件(yaml文件)中的redis环境参数

```properties
# Redis数据库索引（默认为0）
spring.redis.database=0 
# Redis服务器地址
spring.redis.host=localhost
# Redis服务器连接端口
spring.redis.port=6379 
# Redis服务器连接密码（默认为空）
spring.redis.password=123456
#连接池最大连接数（使用负值表示没有限制）
spring.redis.jedis.pool.max-active=8 
# 连接池最大阻塞等待时间（使用负值表示没有限制）
spring.redis.jedis.pool.max-wait=-1 
# 连接池中的最大空闲连接
spring.redis.jedis.pool.max-idle=8 
# 连接池中的最小空闲连接
spring.redis.jedis.pool.min-idle=0 
# 连接超时时间（毫秒）
spring.redis.timeout=300
```

#### 2.配置RedisConfig类

以下配置类直接copy使用即可

```java
/**
 * Redis配置类
 */
@Configuration
@EnableCaching //开启注解
public class RedisConfig extends CachingConfigurerSupport {
    /**
     * retemplate相关配置
     * @param factory
     * @return
     */
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {

        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(factory);

        //使用Jackson2JsonRedisSerializer来序列化和反序列化redis的value值（默认使用JDK的序列化方式）
        Jackson2JsonRedisSerializer jacksonSeial = new Jackson2JsonRedisSerializer(Object.class);

        ObjectMapper om = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会跑出异常
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jacksonSeial.setObjectMapper(om);

        // 值采用json序列化
        template.setValueSerializer(jacksonSeial);
        //使用StringRedisSerializer来序列化和反序列化redis的key值
        template.setKeySerializer(new StringRedisSerializer());

        // 设置hash key 和value序列化模式
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(jacksonSeial);
        template.afterPropertiesSet();

        return template;
    }

    /**
     * 对hash类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public HashOperations<String, String, Object> hashOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForHash();
    }

    /**
     * 对redis字符串类型数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ValueOperations<String, Object> valueOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForValue();
    }

    /**
     * 对链表类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ListOperations<String, Object> listOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForList();
    }

    /**
     * 对无序集合类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public SetOperations<String, Object> setOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForSet();
    }

    /**
     * 对有序集合类型的数据操作
     *
     * @param redisTemplate
     * @return
     */
    @Bean
    public ZSetOperations<String, Object> zSetOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForZSet();
    }
}
```

#### 3.配置RedisUtil类

以下工具类直接copy使用即可

```java
/**
 * TODO(控制RedisTemplate封装)
 */
@Component
public class RedisUtil {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    public RedisUtil(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    /**
     * 指定缓存失效时间
     * @param key 键
     * @param time 时间(秒)
     * @return
     */
    public boolean expire(String key,long time){
        try {
            if(time>0){
                redisTemplate.expire(key, time, TimeUnit.SECONDS);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据key 获取过期时间
     * @param key 键 不能为null
     * @return 时间(秒) 返回0代表为永久有效
     */
    public long getExpire(String key){
        return redisTemplate.getExpire(key,TimeUnit.SECONDS);
    }

    /**
     * 判断key是否存在
     * @param key 键
     * @return true 存在 false不存在
     */
    public boolean hasKey(String key){
        try {
            return redisTemplate.hasKey(key);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除缓存
     * @param key 可以传一个值 或多个
     */
    @SuppressWarnings("unchecked")
    public void del(String ... key){
        if(key!=null&&key.length>0){
            if(key.length==1){
                redisTemplate.delete(key[0]);
            }else{
                redisTemplate.delete(CollectionUtils.arrayToList(key));
            }
        }
    }

    //============================String=============================
    /**
     * 普通缓存获取
     * @param key 键
     * @return 值
     */
    public Object get(String key){
        return key==null?null:redisTemplate.opsForValue().get(key);
    }

    /**
     * 普通缓存放入
     * @param key 键
     * @param value 值
     * @return true成功 false失败
     */
    public boolean set(String key,Object value) {
        try {
            redisTemplate.opsForValue().set(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 普通缓存放入并设置时间
     * @param key 键
     * @param value 值
     * @param time 时间(秒) time要大于0 如果time小于等于0 将设置无限期
     * @return true成功 false 失败
     */
    public boolean set(String key,Object value,long time){
        try {
            if(time>0){
                redisTemplate.opsForValue().set(key, value, time, TimeUnit.SECONDS);
            }else{
                set(key, value);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 递增
     * @param key 键
     * @param delta 要增加几(大于0)
     * @return
     */
    public long incr(String key, long delta){
        if(delta<0){
            throw new RuntimeException("递增因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, delta);
    }

    /**
     * 递减
     * @param key 键
     * @param delta 要减少几(小于0)
     * @return
     */
    public long decr(String key, long delta){
        if(delta<0){
            throw new RuntimeException("递减因子必须大于0");
        }
        return redisTemplate.opsForValue().increment(key, -delta);
    }

    //================================Map=================================
    /**
     * HashGet
     * @param key 键 不能为null
     * @param item 项 不能为null
     * @return 值
     */
    public Object hget(String key,String item){
        return redisTemplate.opsForHash().get(key, item);
    }

    /**
     * 获取hashKey对应的所有键值
     * @param key 键
     * @return 对应的多个键值
     */
    public Map<Object,Object> hmget(String key){
        return redisTemplate.opsForHash().entries(key);
    }

    /**
     * HashSet
     * @param key 键
     * @param map 对应多个键值
     * @return true 成功 false 失败
     */
    public boolean hmset(String key, Map<String,Object> map){
        try {
            redisTemplate.opsForHash().putAll(key, map);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * HashSet 并设置时间
     * @param key 键
     * @param map 对应多个键值
     * @param time 时间(秒)
     * @return true成功 false失败
     */
    public boolean hmset(String key, Map<String,Object> map, long time){
        try {
            redisTemplate.opsForHash().putAll(key, map);
            if(time>0){
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     * @param key 键
     * @param item 项
     * @param value 值
     * @return true 成功 false失败
     */
    public boolean hset(String key,String item,Object value) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 向一张hash表中放入数据,如果不存在将创建
     * @param key 键
     * @param item 项
     * @param value 值
     * @param time 时间(秒)  注意:如果已存在的hash表有时间,这里将会替换原有的时间
     * @return true 成功 false失败
     */
    public boolean hset(String key,String item,Object value,long time) {
        try {
            redisTemplate.opsForHash().put(key, item, value);
            if(time>0){
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 删除hash表中的值
     * @param key 键 不能为null
     * @param item 项 可以使多个 不能为null
     */
    public void hdel(String key, Object... item){
        redisTemplate.opsForHash().delete(key,item);
    }

    /**
     * 判断hash表中是否有该项的值
     * @param key 键 不能为null
     * @param item 项 不能为null
     * @return true 存在 false不存在
     */
    public boolean hHasKey(String key, String item){
        return redisTemplate.opsForHash().hasKey(key, item);
    }

    /**
     * hash递增 如果不存在,就会创建一个 并把新增后的值返回
     * @param key 键
     * @param item 项
     * @param by 要增加几(大于0)
     * @return
     */
    public double hincr(String key, String item,double by){
        return redisTemplate.opsForHash().increment(key, item, by);
    }

    /**
     * hash递减
     * @param key 键
     * @param item 项
     * @param by 要减少记(小于0)
     * @return
     */
    public double hdecr(String key, String item,double by){
        return redisTemplate.opsForHash().increment(key, item,-by);
    }

    //============================set=============================
    /**
     * 根据key获取Set中的所有值
     * @param key 键
     * @return
     */
    public Set<Object> sGet(String key){
        try {
            return redisTemplate.opsForSet().members(key);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 根据value从一个set中查询,是否存在
     * @param key 键
     * @param value 值
     * @return true 存在 false不存在
     */
    public boolean sHasKey(String key,Object value){
        try {
            return redisTemplate.opsForSet().isMember(key, value);
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将数据放入set缓存
     * @param key 键
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSet(String key, Object...values) {
        try {
            return redisTemplate.opsForSet().add(key, values);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 将set数据放入缓存
     * @param key 键
     * @param time 时间(秒)
     * @param values 值 可以是多个
     * @return 成功个数
     */
    public long sSetAndTime(String key,long time,Object...values) {
        try {
            Long count = redisTemplate.opsForSet().add(key, values);
            if(time>0) {
                expire(key, time);
            }
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 获取set缓存的长度
     * @param key 键
     * @return
     */
    public long sGetSetSize(String key){
        try {
            return redisTemplate.opsForSet().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 移除值为value的
     * @param key 键
     * @param values 值 可以是多个
     * @return 移除的个数
     */
    public long setRemove(String key, Object ...values) {
        try {
            Long count = redisTemplate.opsForSet().remove(key, values);
            return count;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
    //===============================list=================================

    /**
     * 获取list缓存的内容
     * @param key 键
     * @param start 开始
     * @param end 结束  0 到 -1代表所有值
     * @return
     */
    public List<Object> lGet(String key, long start, long end){
        try {
            return redisTemplate.opsForList().range(key, start, end);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 获取list缓存的长度
     * @param key 键
     * @return
     */
    public long lGetListSize(String key){
        try {
            return redisTemplate.opsForList().size(key);
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }

    /**
     * 通过索引 获取list中的值
     * @param key 键
     * @param index 索引  index>=0时， 0 表头，1 第二个元素，依次类推；index<0时，-1，表尾，-2倒数第二个元素，依次类推
     * @return
     */
    public Object lGetIndex(String key,long index){
        try {
            return redisTemplate.opsForList().index(key, index);
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, Object value) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @param time 时间(秒)
     * @return
     */
    public boolean lSet(String key, Object value, long time) {
        try {
            redisTemplate.opsForList().rightPush(key, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @return
     */
    public boolean lSet(String key, List<Object> value) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 将list放入缓存
     * @param key 键
     * @param value 值
     * @param time 时间(秒)
     * @return
     */
    public boolean lSet(String key, List<Object> value, long time) {
        try {
            redisTemplate.opsForList().rightPushAll(key, value);
            if (time > 0) {
                expire(key, time);
            }
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 根据索引修改list中的某条数据
     * @param key 键
     * @param index 索引
     * @param value 值
     * @return
     */
    public boolean lUpdateIndex(String key, long index,Object value) {
        try {
            redisTemplate.opsForList().set(key, index, value);
            return true;
        } catch (Exception e) {
            e.printStackTrace();
            return false;
        }
    }

    /**
     * 移除N个值为value
     * @param key 键
     * @param count 移除多少个
     * @param value 值
     * @return 移除的个数
     */
    public long lRemove(String key,long count,Object value) {
        try {
            Long remove = redisTemplate.opsForList().remove(key, count, value);
            return remove;
        } catch (Exception e) {
            e.printStackTrace();
            return 0;
        }
    }
}
```

#### 4.创建实体类

```java
public class User implements Serializable {
    private Integer id;

    private String username;

    private String password;

    public Integer getId() {
        return id;
    }
    ...
}
```

**其中实体类必须继承其Serializable 类实现其序列化**

#### 5.在Controller中使用（读写）

```java
	private static int ExpireTime = 60;  //设置redis存储的过期时间为60s

	@Resource
    private RedisUtil redisUtil;

	/**
     * 写入数据至Redis
     * @param key Key值
     * @param value
     * @return
     */
    @RequestMapping("set")
    public boolean redisSet(String key,String value){
        User user=new User();
        user.setId(6);
        user.setUsername("Ray");
        user.setPassword("55151");
        return redisUtil.set(key,user,ExpireTime);
    }

	/**
     * 读取Redis中的键值
     * @param key Key值
     * @return
     */
    @RequestMapping("get")
    public Object redisget(String key){
        return redisUtil.get(key);
    }
```

<img src="picture\image-20201029161405396.png" style="zoom: 50%;" />

<img src="picture\image-20201029161530271.png" style="zoom: 67%;" />

