---
title: Spring Framework
created: 2022-10-01 10:36:01
modified: 2023-03-26 11:19:47
aliases: [Spring 框架]
tags: [CS, Java]
---

## Spring Bean 生命周期

-   实例化 Bean 对象。
-   设置 Bean 属性。
-   如果我们通过各种 Aware 接口声明了依赖关系，则会注入 Bean 对容器基础设施层面的依赖。具体包括 BeanNameAware、BeanFactoryAware 和 ApplicationContextAware，分别会注入 Bean ID、Bean Factory 或者 ApplicationContext。
-   调用 BeanPostProcessor 的前置初始化方法 postProcessBeforeInitialization。
-   如果实现了 InitializingBean 接口，则会调用 afterPropertiesSet 方法。
-   调用 Bean 自身定义的 init 方法。
-   调用 BeanPostProcessor 的后置初始化方法 postProcessAfterInitialization。
-   创建过程完毕。

![[../images/spring-bean-lifecycle.png]]

## 常见问题

1. 了解Spring约定的默认扫描域
2. 注意Bean的构造方法
3. 注意原型作用域的Bean与Autowired的搭配

### 原型Bean被固定

```java
// 原型Scopde的Bean
@Service
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class ServiceImpl{

}

// 使用案例
@RestController
public class HelloWorldController {

    @Autowired
    private ServiceImpl svcImpl;

    @GetMapping("hi")
    public String hi() {
        return "hi, service is: " + this.scvImpl;
    }
}
```

现象是，该接口始终返回同一个地址的ServiceImpl；定义的Prototype Scope未生效。

分析：当一个属性成员被声明为 `@Autowired` 后，在创建该类Bean时，会先使用构造器反射出实例，然后再装配各个标记为 `@Autowired` 的属性成员（`AbstractAutowireCapableBeanFactory#populateBean`）

待找到要自动注入的 Bean 后，即可通过反射设置给对应的 field。这个 field 的 set 只发生了一次，所以后续就固定起来了，它并不会因为 `ServiceImpl` 标记了
SCOPE_PROTOTYPE 而改变。

#### 解决方案

**自动注入Context**

```java
HelloWorldController {

    @Autowired
    private ApplicationContext ac;

    ...

    public ServiceImpl getSvcImpl() {
        return this.ac.getBean(ServiceImpl.class);
    }
}
```

**通过LookUp注解**

```java
...
    @Lookup
    public ServiceImpl getSvcImpl(){
    // 当使用 Lookup 注解一个方法时，这个方法的具体实现已并不重要。
        return null;
    }
...
```

### Autowired 使用

Autowired 默认是 required 模式，Bean的数量不能为0，也不能大于1。

Bean的默认名称规则：如果一个类名是以两个大写字母开头的，则首字母不变，其它情况下默认首字母变成小写。

如何引用内部类的Bean：`studentController.InnerClassDataService` (`ClassUtils.getShortName(beanClassName)`)

### Value 使用

不仅要避免和环境变量冲突，也要注意避免和系统变量等其他变量冲突。

```java
// 使用 @Value 装配成员属性
@Value("#{student}")
private Student student;

@Value("sample string")
private String text;

// 注入系统参数、环境变量或配置文件中的值
@Value("${ip}")
private String ip;

// 注入其他Bean属性
@Value("#{student.name}")
private String name;
```

### Spring 启动

1. 使用 @Autowired 直接标记在成员属性上而引发的装配行为是发生在构造器执行之后的。
2. 避免在 Java 类中定义一些带有特殊意义动词的方法来解决 (shutdown, close)

![[../images/spring-life-cycle.png]]

#### TOMCAT

![[../images/tomcat-stacktrace.png]]

### Spring AOP

只有引用的是被动态代理创建出来的对象，才会被 Spring 增强，具备 AOP 该有的功能

通过反射来实例化的三种方式：

- `java.lang.Class.newInsance()`
- `java.lang.reflect.Constructor.newInstance()`
- `sun.reflect.ReflectionFactory.newConstructorForSerialization().newInstance()`

![[../images/spring-aop-lifecycle.png]]

通过 `@Autowired` 可以获取该Bean的代理增强类

```java
@Service
public class ElectricService {

    @Autowired
    private ElectricService es;
    
    public void charge() {
        sout("charging...");
        // this 是当前类的普通对象，不受aop监管
        // this.pay();
        // 自动注入的对象是proxy代理类
        this.es.pay();
    }
    
    public void pay() {
        sout("paying...");
        Thread.sleep(1000L);
    }
}

```

1. 使用 AOP，实际上就是让 Spring 自动为我们创建一个 Proxy，使得调用者能无感知地调用指定方法。而 Spring 有助于我们在运行期里动态织入其它逻辑，因此，AOP 本质上就是一个动态代理。
2. 我们只有访问这些代理对象的方法，才能获得 AOP 实现的功能，所以通过 this 引用是无法正确使用 AOP 功能的。在不能改变代码结果前提下，我们可以通过@Autowired、AopContext.currentProxy() 等方式获取相应的代理对象来实现所需的功能。
3. 我们一般不能直接从代理类中去拿被代理类的属性，这是因为除非我们显示设置spring.objenesis.ignore 为 true，否则代理类的属性是不会被 Spring 初始化的，我们可以通过在被代理类中增加一个方法来间接获取其属性。

在同一个切面配置中，如果存在多个不同类型的增强，那么其执行优先级是按照增强类型的特定顺序排列，依次的增强类型为 Around.class, Before.class, After.class,AfterReturning.class, AfterThrowing.class；

在同一个切面配置中，如果存在多个相同类型的增强，那么其执行优先级是按照该增强的方法名排序，排序方式依次为比较方法名的每一个字母，直到发现第一个不相同且 ASCII 码较小的字母。

### Spring Listener

**对于事件一定要注意"驴头"（监听器）对上"马嘴"（广播）。**

#### initialMulticaster

`ApplicationEnvironmentPreparedEvent` 与其相关的两大组件：

1. 广播器：`EventPublishingRunListener#initialMulticaster`
2. 监听器：`EventPublishingRunListener#(this.initialMulticaster.addApplicationListener(listener))`

上述在构建 EventPublisingRunListener 时，构造方法中传递的 listeners 来自于 `SpringFactoriesLoader#loadSpringFactories`，即 `spring.factories`

与用户自定义成 `@Component` 的 listener 是两套体系；故无法拦截到该事件；正确做法是，将自定义的 Listener 添加到 `spring.factories` 中。

### RequestParam

将参数设置为非必填的四种方法

1. 添加默认值 `@RequestParam(value = "address", defaultValue = "no address") String address`
2. 设置 required 参数 `@RequestParam(value = "address", required = false) String address)`
3. 标记任何名为 Nullable 且 RetentionPolicy 为 RUNTIME 的注解 `org.springframework.lang.Nullable`
4. 修改参数类型为 Optional `@RequestParam(value = "address") Optionaladdress`

### Spring Body

不同的 Body 需要不同的编解码器，而使用哪一种是协商出来的，协商过程大体如下：

1. 查看请求头中是否有 ACCET 头，如果没有则可以使用任何类型；
2. 查看当前针对返回类型（即 Student 实例）可以采用的编码类型；
3. 取上面两步获取的结果的交集来决定用什么方式返回。

在尝试读取 HTTP Body 时，你要注意到 Body 本身是一个流对象，不能被多次读取。

### Spring Dispatch

![[../images/spring-dispatch-workflow.png]]

### Spring Validation

```java
public class User {
    @Size(max = 10)
    private String name;
    // 默认 @Validated 校验不会 cascade
    @Valid
    // 通过指定 @Valid，实现多层级校验
    private Phone phone;
}

public class Phone {
    @Size(max = 11)
    private String number;
}
```

### Spring MVC Filter

WebFilter 的全名是 javax.servlet.annotation.WebFilter，很明显，它并不属于Spring，而是 Servlet 的规范。当 Spring Boot 项目中使用它时，Spring Boot 使用了`org.springframework.boot.web.servlet.FilterRegistrationBean` 来包装 @WebFilter 标
记的实例。

本质上，过滤器被 @WebFilter 修饰后，TimeCostFilter 只会被包装为 `FilterRegistrationBean` ，而 TimeCostFilter 自身，只会作为一个 InnerBean 被实例化，这意味着 TimeCostFilter 实例并不会作为 Bean 注册到 Spring 容器。

## References

 - Spring常见业务问题 - 极客时间
