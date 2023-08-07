# SpringRefresh

## 主要总结

`SpringApplicationContext `简单来说它就是Spring中的容器，可以用来获取容器中的各种bean组件，注册监听事件，加载资源文件等功能。它是Spring的重要组件之一

`refresh` 是 AbstractApplicationContext 中的一个方法，负责初始化SpringApplicationContext 容器。在每次运行Spring应用时都会被自动调用。

`refresh` 内部主要会调用 12 个方法，我们把它们称为 refresh 的 12 个步骤：

1. prepareRefresh - 做好准备工作
2. obtainFreshBeanFactory - 创建或获取BeanFactory
3. prepareBeanFactory - 准备BeanFactory
4. postProcessBeanFactory - 子类扩展BeanFactory
5. invokeBeanFactoryPostProcessors - 后处理器BeanFactory
6. registerBeanPostProcessors - 准备Bean后处理器
7. initMessageSource - 为ApplicationContext提供国际化功能
8. initApplicationEventMulticaster - ApplicationContext提供事件发布器
9. OnRefresh - 留给子类扩展
10. registerListeners - 为ApplicationContext准备监听器
11. finishBeanFactoryInitialization - 初始化单例Bean，执行Bean后处理器扩展
12. finishRefresh - 准备生命周期管理器，发布ContextRefreshed事件

## 个别详解

prepareRefresh 准备了Environment环境对象。

```java
public class EnvironmentTest {
    public static void main(String[] args) throws NoSuchFieldException {
        System.out.println("------仅获取@Value值------");
        QualifierAnnotationAutowireCandidateResolver resolver = new QualifierAnnotationAutowireCandidateResolver();
        Object name = resolver.getSuggestedValue(new DependencyDescriptor(Bean1.class.getDeclaredField("name"), false));
        System.out.println(name);
        System.out.println("------仅获取@Value值,并解析${}------");
        Object javaHome = resolver.getSuggestedValue(new DependencyDescriptor(Bean1.class.getDeclaredField("JavaHome"), false));
        System.out.println(getEnvironment().resolvePlaceholders(javaHome.toString()));
    }

    private static Environment getEnvironment(){
        StandardEnvironment environment = new StandardEnvironment();
        return environment;
    }

    static class Bean1 {
        @Value("ZTY")
        private String name;

        @Value("${Path}")
        private String JavaHome;
        
    }
}
```

StandardEnvironment获取到当前的Java环境配置，通过它的`resolvePlaceholders`方法可以进行解析`${}`内容。



obtainFreshBeanFactory 这步会将其获取BeanFactory，之后会在其内进行注册`BeanDefinition`。

`BeanFactory`作用是负责bean的创建、依赖注入和初始化。

`BeanDefinition`是对与一个Bean的设计定义，规定了Bean的特征，如单例多例、依赖关系、初始销毁方法等。而`BeanDefinition`可以通过XML文件、配置类、包扫描、BeanFactory后处理器等获取。

```java
public class BeanFactoryTest {

    public static void main(String[] args) throws IOException {
        DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();
        System.out.println("------使用XML添加Bean------");
        XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);
        beanDefinitionReader.loadBeanDefinitions(new ClassPathResource("test.xml"));
        System.out.println(Arrays.toString(beanFactory.getBeanDefinitionNames()));
        System.out.println("------从配置中添加Bean------");
        beanFactory.registerBeanDefinition("config1", BeanDefinitionBuilder.genericBeanDefinition(Config1.class).getBeanDefinition());
        System.out.println(Arrays.toString(beanFactory.getBeanDefinitionNames()));
        System.out.println("------使用BeanFactory后处理器添加Bean内部中的Bean------");
        ConfigurationClassPostProcessor postProcessor = new ConfigurationClassPostProcessor();
        postProcessor.postProcessBeanDefinitionRegistry(beanFactory);
        System.out.println(Arrays.toString(beanFactory.getBeanDefinitionNames()));

//        System.out.println("------包扫描添加Bean------");
//        ClassPathBeanDefinitionScanner scanner = new ClassPathBeanDefinitionScanner(beanFactory);
//        scanner.scan("org.zssaer.autoconfigura");
//        System.out.println(Arrays.toString(beanFactory.getBeanDefinitionNames()));

    }

    static class Bean1 {

    }

    static class Bean2 {

    }

    static class Config1 {
        @Bean
        public Bean2 bean2() {
            return new Bean2();
        }
    }
}
```

BeanFactory后处理器可以充当BeanFactory的扩展点，可以用来补充或修改BeanDefinition。

其中`ConfigurationClassPostProcessor`是一个常见的BeanFactoryPostProcessor，它用来解析已添加的Bean中的类含有@Configuration、@Bean、@Import、@PropertySource，并将其以BeanDefinition注册到BeanFactory。





registerBeanProcessors这步将注册Bean后处理器。Bean后处理器充当Bean的扩展点，可以工作在bean的实例化、依赖注入、初始化阶段。

所有的Bean后处理器都同样需要提前在BeanFactory以注册BeanDefinition方式进行注册。

```java
public class BeanPostProcessorTest {
    public static void main(String[] args) {
        GenericApplicationContext context = new GenericApplicationContext();
        DefaultListableBeanFactory beanFactory = context.getDefaultListableBeanFactory();
        beanFactory.registerBeanDefinition("bean1", BeanDefinitionBuilder.genericBeanDefinition(Bean1.class).getBeanDefinition());
        beanFactory.registerBeanDefinition("bean2", BeanDefinitionBuilder.genericBeanDefinition(Bean2.class).getBeanDefinition());
        beanFactory.registerBeanDefinition("bean3", BeanDefinitionBuilder.genericBeanDefinition(Bean3.class).getBeanDefinition());
        beanFactory.registerBeanDefinition("aspect1", BeanDefinitionBuilder.genericBeanDefinition(Aspect1.class).getBeanDefinition());
        // 向BeanFactory注册Autowired的BeanPostProcessor
        beanFactory.registerBeanDefinition("processor1", BeanDefinitionBuilder.genericBeanDefinition(AutowiredAnnotationBeanPostProcessor.class).getBeanDefinition());
        // 向BeanFactory注册Resource的BeanPostProcessor
        beanFactory.registerBeanDefinition("processor2", BeanDefinitionBuilder.genericBeanDefinition(CommonAnnotationBeanPostProcessor.class).getBeanDefinition());
        // 向BeanFactory注册Aspect的BeanPostProcessor
        beanFactory.registerBeanDefinition("processor3", BeanDefinitionBuilder.genericBeanDefinition(AnnotationAwareAspectJAutoProxyCreator.class).getBeanDefinition());
        context.refresh();
        beanFactory.getBean(Bean1.class).foo();

    }

    static class Bean1 {
        Bean2 bean2;
        Bean3 bean3;
        
        @Autowired
        public void setBean2(Bean2 bean2) {
            System.out.println("发生了依赖注入..."+bean2);
            this.bean2 = bean2;
        }
        @Resource
        public void setBean3(Bean3 bean3) {
            System.out.println("发生了依赖注入..."+bean3);
            this.bean3 = bean3;
        }

        public void foo(){
            System.out.println("foo");
        }
    }

    static class Bean2 {
    }

    static class Bean3 {
    }

    @Aspect
    static class Aspect1 {
        @Before("execution(* foo())")
        public void before() {
            System.out.println("before...");
        }
    }
```

这里其中的AutowiredAnnotationBeanPostProcessor功能是解析@Autowired、@Value注解。实现依赖注入。

而CommonAnnotationBeanPostProcessor功能是解析@Resouce以实现依赖注入、@PostConstruct、@PreDestroy 以实现Bean初始化销毁操作。

AnnotationAwareAspectJAutoProxyCreator功能是实现AspectJ切面代理操作。

