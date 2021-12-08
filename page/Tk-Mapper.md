# TKMybatis-通用Mapper

TkMybatis，中文名称叫通用Mapper，是一款能够实现自定义Java处理的Mybatis第三方插件，在项目中使用通用Mapper能够轻松实现自定义的CURD等Mybatis操作。与它类似的插件还有MybatisPlus等。

通用Mapper的本质的实现是利用了 MyBatis 代码生成器 + JPA注释操作。

## 生成代码

```java
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>2.2.0</version>
		</dependency>
		<!-- TK-Mybatis -->
			<dependency>
  			<groupId>tk.mybatis</groupId>
  			<artifactId>mapper-spring-boot-starter</artifactId>
  			<version>2.1.5</version>
		</dependency>
```

使用通用Mapper时，可以和平常的Mybatis一样，先使用MyBatis 代码生成器生成Mapper，其中我们向其中的MyBatis 代码生成配置中配置通用Mapper插件。

```xml
<generatorConfiguration>
    <context id="sakila" targetRuntime="MyBatis3">
        ...
        <!-- 使用tkMapper插件 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <!-- value可以为默认的,也可以为用户定义的tkMapper实体类 -->
            <property name="mappers" value="tk.mybatis.mapper.common.Mapper"/>
            <!-- caseSensitive默认false，当数据库表名区分大小写时，可以将该属性设置为true -->
            <property name="caseSensitive" value="true"/>
        </plugin>
        ...
    </context>
</generatorConfiguration>
```

其中配置中`tk.mybatis.mapper.common.Mapper`是TkMapper中的一个通用Mapper类接口，我们可以查看具体的内容。

```java
public interface Mapper<T> extends
        BaseMapper<T>,
        ExampleMapper<T>,
        RowBoundsMapper<T>,
        Marker {
}
```

为了实现更好的功能，推荐大家自己本地项目中配置一个通用Mapper类接口，这是我用的最习惯的配置：

```java
public interface Mapper<T>
        extends
        BaseMapper<T>,
        ConditionMapper<T>,
        IdsMapper<T>,
        InsertListMapper<T> {
}
```

然后将其配置中`tk.mybatis.mapper.common.Mapper`更换为自己配置的这个类接口包名接口。



生成Mybatis代码完毕后，我们会发现，生成的mapper接口都继承上了这个通用Mapper接口，表示它就可以使用Tkmapper的功能了。

```java
public interface XxxMapper extends Mapper<Xxx> {
}
```

### 配置文件配置

和Mybatis一样，我们在使用前必须配置相关配置，才能保证其插件正常启用。

由于这里面我们引进的是`mapper-spring-boot-starter`这个项目包，它内部拥有SpringBoot自动配置的配置，所以我们可以直接在SpringBoot配置文件中进行配置：

```xml-dtd
mapper:
  mappers:
    - tk.mybatis.mapper.common.Mapper
    - tk.mybatis.mapper.common.Mapper2
  notEmpty: true
```

**注意：引入该 starter 时，和 MyBatis 官方的 starter 没有冲突，但是官方的自动配置不会生效！**

我们也可以导入原始版本的Tk-Mybatis的依赖：

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper</artifactId>
    <version>4.1.5</version>
</dependency>
```

它的配置复杂点，在SpringBoot中需要进行设置配置类：

```java
@Configuration
public class MybatisConfigurer {
	...
	@Bean
    public MapperScannerConfigurer mapperScannerConfigurer() {
    	//下面配置通用Mapper，详情请查阅官方文档
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        mapperScannerConfigurer.setSqlSessionFactoryBeanName("sqlSessionFactoryBean");
        // 设置Mapper接口包
        mapperScannerConfigurer.setBasePackage(MAPPER_PACKAGE);

        Properties properties = new Properties();
        // 设置通用Mapper接口类包名
        properties.setProperty("mappers", MAPPER_INTERFACE_REFERENCE);
        properties.setProperty("notEmpty", "false");//insert、update是否判断字符串类型!='' 即 test="str != null"表达式内是否追加 and str != ''
        properties.setProperty("IDENTITY", "MYSQL");
        mapperScannerConfigurer.setProperties(properties);

        return mapperScannerConfigurer;
    }
}
```

上述的MapperScannerConfigurer 皆为`tk.mybatis.spring.mapper`包下，而不不是官方Mybatis包下的。

### 配置介绍

上面的配置文件配置中，我们配置了一些基础配置，一般情况下即可满足使用。下面我们简绍具体配置以及用途：

***mappers***：

在 4.0 以前这是一个非常重要的参数，当时只有通过 自定义mappers 配置过的接口才能真正调用，由于很多人不注意看文档，通用 Mapper 90% 的问题都出在这个参数上。

4.0 之后，增加了一个 `@RegisterMapper` 注解，通用 Mapper 中提供的所有接口都有这个注解，有了该注解后，通用 Mapper 会自动解析所有的接口，如果父接口（递归向上找到的最顶层）存在标记该注解的接口，就会自动注册上。因此 4.0 后使用通用 Mapper 提供的方法时，不需要在配置这个参数。

当你自己自定义通用接口时，建议加上`@RegisterMapper`注解，否则就要配置 `mappers` 参数。



***IDENTITY***:

取回主键的方式，可以配置的值如下中所列的数据库类型：

- **DB2**:
- **MYSQL**
- **SQLSERVER**
- **CLOUDSCAPE**
- **DERBY**
- **HSQLDB**
- **SYBASE**
- **DB2_MF**
- **INFORMIX**

只有定义这个后再实体类中，主键属性上的`@GeneratedValue(strategy = GenerationType.IDENTITY)` 定义才有效。



***catalog、schema***：

数据库的`catalog`、`schema`，如果设置该值，查询的时候表名会带`catalog`或者`schema`设置的前缀。其中`catalog`优先级高于`schema`。



***notEmpty***：

接受一个布朗值，`insertSelective` 和 `updateByPrimaryKeySelective` 中，是否判断字符串类型 `!=''`。



...通用Mapper中还有很多配置，但是用处不大，这里不做过多简绍了。

### 使用

我们完成上述操作后我们可以在Service中注入Mapper，就可以使用TkMapper的快速功能了。

### 常规使用

`select(T record)`: 根据实体中的属性值进行精准查询，它接受一个相关类型的实体类，返回相关类型实体集合。

注意: 查询操作不建议大伙使用这种方式,查询推荐使用条件查询方式，后面简绍。

```java
@Resource
private AdminMapper adminMapper;
...
Admin admin = new Admin();
// 查询 sex = 0
admin.setSex = 0;
List<Admin> admins = adminMapper.select(admin);
```

`selectByPrimaryKey(Object key)`: 根据实体中的主键进行精准查询，它接受一个主键相关类型的值，返回一个实体类。

```java
@Resource
private AdminMapper adminMapper;
...
Admin admin = adminMapper.selectByPrimaryKey(12);
```

`selectAll()` :查询出所有的实体类 数据。

```java
@Resource
private AdminMapper adminMapper;
...
List<Admin> admins =  adminMapper.selectAll();
```

`insert(T record)` : 向实体类数据库中插入一条数据。它接受一个相关类型的实体类,返回一个int,表示成功插入个数。

```java
@Resource
private AdminMapper adminMapper;
...
Admin admin = new Admin();
adminMapper.insert(admin)
```

`insertList(List<T> recordList)` : 向实体类数据库中插多条数据。它接受一个相关类型的List集合,返回一个int,表示成功插入个数。

```java
@Resource
private AdminMapper adminMapper;
...
List<Admin> admins = Arrays.asList(new Admin(),new Admin(),new Admin())
adminMapper.insert(admins)
```

`insertSelective(T record)`: 向实体类数据库中插入一条数据，其中null的属性不会保存，会使用数据库默认值。它接受一个相关类型的实体类，返回一个int，表示成功插入个数。

```java
@Resource
private AdminMapper adminMapper;
...
Admin admin = new Admin();
adminMapper.insertSelective(admin)
```

`updateByPrimaryKey(T record)` : 根据主键更新指定实体数据库实体，null值也会被更新。

```java
@Resource
private AdminMapper adminMapper;
...
Admin admin = new Admin();
// 更新id为12的实体
admin.setId = 12;
admin.setName= Zssaer;
adminMapper.updateByPrimaryKey(admin)
```

`delete(T record)` : 根据相关属性,删除一个具体的实体类。返回一个int,表示成功删除个数。

注意:其中的删除的实体类属性必须完全一致。大部分时间我们不会常用这个方法来删除，我们使用条件查询方式删除，这个后面简绍。

```java
@Resource
private AdminMapper adminMapper;
...
Admin admin = new Admin();

admin.setId = 12;
admin.setName= Zssaer;
adminMapper.delete(admin)
```

`deleteByPrimaryKey(Object key)`:根据实体类主键删除相关实体类。

```java
@Resource
private AdminMapper adminMapper;
...
adminMapper.deleteByPrimaryKey(12);
```



`existsWithPrimaryKey(Object key)` : 通过实体类主键来判断是否存在该主键实体，返回一个boolean值。

```java
@Resource
private AdminMapper adminMapper;
...
if(adminMapper.existsWithPrimaryKey(12)){
    ...
}
```



### 条件查询使用

上面介绍了部分常规使用的方法，它们包含了部分简易的CRUD操作，使用它们可以快速进行一些CRUD操作。但是对于一些复杂的查询、删除等操作，我们通常会使用条件查询使用的方法。

#### Example

通用 Mapper 中的 Example 方法有两大类定义，一个参数和两个参数的，例如下面两个：

```java
List<T> selectByExample(Object example);

int updateByExampleSelective(@Param("record") T record, @Param("example") Object example);
```

Example 类 ，它接受一个实体类的类，表示该Example 为 该实体类的Example 类。

```java
Example  exa = new Example (Admin.class);
```

有了对于实体类的Example 类后就可以进行相关的条件查询了。

下面是一些例子：

```java
Example example = new Example(Country.class);
// 设置为 for update
example.setForUpdate(true);
// 创建 条件查询为 id > 100 并且id < 151
example.createCriteria().andGreaterThan("id", 100).andLessThan("id",151);
// 或者 id <41
example.or().andLessThan("id", 41);
```

其中createCriteria方法相当于where，上述Example 类相当于拼接出了一段Sql语句:

```java
SELECT id,countryname,countrycode FROM country WHERE ( id > 100 and id < 151 ) or ( id < 41 ) ORDER BY id desc FOR UPDATE 
```

如果未指定排序方式的话，自动为 主键 id 倒序。

最后将其创建好的Example类放置到`selectByExample`中即可实现自定义查询 :

```java
List<Admin> admins =  adminMapper.selectByExample(example);
```



我们也可以将其创建好的Example类放置到`deleteByCondition`中即可实现自定义查询后删除 :

```java
adminMapper.deleteByCondition(example);
```

#### Condition

Condition是通用Mapper中的另一个条件查询类，它的使用方法和Example一样，它的存在只是为了避免Example带来的歧义。它的自定义查询实现为`selectByCondition`,自定义删除实现为`deleteByCondition`...



#### 动态SQl查询

动态SQL查询，在实际项目中非常常见，比如实现聚合查询功能。它根据其请求来进行动态拼接SQL查询：

```java
public Result list(ArticleReq req) {
        PageHelper.startPage(req.getPage(), req.getSize());

        Integer id = req.getId();
        Integer type = req.getArticleType();
        String title = req.getArticleTitle();
        Integer status = req.getStatus();
        String lang = req.getLang();

        Condition condition = new Condition(Article.class);
        Example.Criteria criteria = condition.createCriteria();

        if (id != null) {
            criteria.andEqualTo("id", id);
        }
        if (type != null) {
            criteria.andEqualTo("articleType", type);
        }
        if (!StringUtils.isEmpty(title)) {
            criteria.andLike("articleTitle", '%' + title + '%');
        }
        if (status != null) {
            criteria.andEqualTo("status", status);
        }
        if (!StringUtils.isEmpty(lang)) {
            criteria.andEqualTo("lang", lang);
        }
    	List<Article> articleList = articleService.findByCondition(condition);
    	...
}
```

#### 排序

```java
Example example = new Example(Country.class);
example.orderBy("id").desc().orderBy("countryname").orderBy("countrycode").asc();
List<Country> countries = mapper.selectByExample(example);
```

结果SQL为：

```sql
SELECT id,countryname,countrycode FROM country order by id DESC,countryname,countrycode ASC 
```

当然还可以手动写排序：

```java
example.setOrderByClause("is_top desc,news_time desc");
```

#### 去重

```java
Example example = new Example(Country.class);
//设置 distinct
example.setDistinct(true);
```



#### 总结

总结一下其中不管是Example还是Condition，他们的主要使用方法：

| 方法                                       | 说明                                          |
| ------------------------------------------ | --------------------------------------------- |
| example.setOrderByClause(“字段名 ASC”)     | 添加升序排列条件，DESC为降序                  |
| example.setDistinct(false)                 | 去除重复，boolean型，true为选择不重复的记录。 |
| criteria.andXxxIsNull                      | 添加字段xxx为null的条件                       |
| criteria.andXxxIsNotNull                   | 添加字段xxx不为null的条件                     |
| criteria.andXxxEqualTo(value)              | 添加xxx字段等于value条件                      |
| criteria.andXxxNotEqualTo(value)           | 添加xxx字段不等于value条件                    |
| criteria.andXxxGreaterThan(value)          | 添加xxx字段大于value条件                      |
| criteria.andXxxGreaterThanOrEqualTo(value) | 添加xxx字段大于等于value条件                  |
| criteria.andXxxLessThan(value)             | 添加xxx字段小于value条件                      |
| criteria.andXxxLessThanOrEqualTo(value)    | 添加xxx字段小于等于value条件                  |
| criteria.andXxxIn(List<？>)                | 添加xxx字段值在List<？>条件                   |
| criteria.andXxxNotIn(List<？>)             | 添加xxx字段值不在List<？>条件                 |
| criteria.andXxxNotIn(List<？>)             | 添加xxx字段值不在List<？>条件                 |

