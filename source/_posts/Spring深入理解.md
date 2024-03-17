---
title: Spring深入理解
tags:
  - spring
categories:
  - spring
keywords: spring;
index_img: /img/spring/spring.jpg
excerpt: 深入理解spring核心；容器和bean、AOP、MVC、springboot。
abbrlink: 8358769a
date: 2019-08-25 21:15:49
---

## Spring基础

### 什么是spring框架

Spring 是一款开源的轻量级 Java 开发框架，旨在提高开发人员的开发效率以及系统的可维护性。

我们一般说 Spring 框架指的都是 Spring Framework，它是很多模块的集合，使用这些模块可以很方便地协助我们进行开发，比如说 Spring 支持 IoC（Inverse of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141045694.png)

Spring 最核心的思想就是不重新造轮子，开箱即用，提高开发效率。
Spring 提供的核心功能主要是 IoC 和 AOP。学习 Spring ，一定要把 IoC 和 AOP 的核心思想搞懂！

### Spring 主要模块

Spring4.x版本

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141046323.png)

spring5.x版本

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141047202.png)

Spring5.x 版本中 Web 模块的 Portlet 组件已经被废弃掉，同时增加了用于异步响应式处理的 WebFlux 组件。

Spring 各个模块的依赖关系

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141047722.png)


### Core Container

Spring 框架的核心模块，也可以说是基础模块，主要提供 IoC 依赖注入功能的支持。Spring 其他所有的功能基本都需要依赖于该模块，我们从上面那张 Spring 各个模块的依赖关系图就可以看出来。

- **spring-core** ：Spring 框架基本的核心工具类。
- **spring-beans** ：提供对 bean 的创建、配置和管理等功能的支持。
- **spring-context** ：提供对国际化、事件传播、资源加载等功能的支持。
- **spring-expression** ：提供对表达式语言（Spring Expression Language） SpEL 的支持，只依赖于 core 模块，不依赖于其他模块，可以单独使用。

### AOP

- **spring-aspects** ：该模块为与 AspectJ 的集成提供支持。
- **spring-aop** ：提供了面向切面的编程实现。
- **spring-instrument** ：提供了为 JVM 添加代理（agent）的功能。 具体来讲，它为 Tomcat 提供了一个织入代理，能够为 Tomcat 传递类文 件，就像这些文件是被类加载器加载的一样。没有理解也没关系，这个模块的使用场景非常有限。

### Data Access/Integration

- **spring-jdbc** ：提供了对数据库访问的抽象 JDBC。不同的数据库都有自己独立的 API 用于操作数据库，而 Java 程序只需要和 JDBC API 交互，这样就屏蔽了数据库的影响。
- **spring-tx** ：提供对事务的支持。
- **spring-orm** ： 提供对 Hibernate、JPA 、iBatis 等 ORM 框架的支持。
- **spring-oxm** ：提供一个抽象层支撑 OXM(Object-to-XML-Mapping)，例如：JAXB、Castor、XMLBeans、JiBX 和 XStream 等。
- **spring-jms** : 消息服务。自 Spring Framework 4.1 以后，它还提供了对 spring-messaging 模块的继承。

### Spring Web

- **spring-web** ：对 Web 功能的实现提供一些最基础的支持。
- **spring-webmvc** ： 提供对 Spring MVC 的实现。
- **spring-websocket** ： 提供了对 WebSocket 的支持，WebSocket 可以让客户端和服务端进行双向通信。
- **spring-webflux** ：提供对 WebFlux 的支持。WebFlux 是 Spring Framework 5.0 中引入的新的响应式框架。与 Spring MVC 不同，它不需要 Servlet API，是完全异步。

### Messaging

**spring-messaging** 是从 Spring4.0 开始新加入的一个模块，主要职责是为 Spring 框架集成一些基础的报文传送应用。

### Spring Test

Spring 团队提倡测试驱动开发（TDD）。有了控制反转 (IoC)的帮助，单元测试和集成测试变得更简单。

Spring 的测试模块对 JUnit（单元测试框架）、TestNG（类似 JUnit）、Mockito（主要用来 Mock 对象）、PowerMock（解决 Mockito 的问题比如无法模拟 final, static， private 方法）等等常用的测试框架支持的都比较好。

### Spring,Spring MVC,Spring Boot 之间的关系

很多人对 Spring,Spring MVC,Spring Boot 这三者傻傻分不清楚！这里简单介绍一下这三者，其实很简单，没有什么高深的东西。

Spring 包含了多个功能模块（上面刚刚提高过），其中最重要的是 Spring-Core（主要提供 IoC 依赖注入功能的支持） 模块， Spring 中的其他模块（比如 Spring MVC）的功能实现基本都需要依赖于该模块。

Spring MVC 是 Spring 中的一个很重要的模块，主要赋予 Spring 快速构建 MVC 架构的 Web 程序的能力。MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141053129.png)

使用 Spring 进行开发各种配置过于麻烦比如开启某些 Spring 特性时，需要用 XML 或 Java 进行显式配置。于是，Spring Boot 诞生了！

Spring 旨在简化 J2EE 企业应用程序开发。Spring Boot 旨在简化 Spring 开发（减少配置文件，开箱即用！）。

Spring Boot 只是简化了配置，如果你需要构建 MVC 架构的 Web 程序，你还是需要使用 Spring MVC 作为 MVC 框架，只是说 Spring Boot 帮你简化了 Spring MVC 的很多配置，真正做到开箱即用！

## Spring IOC

### 对Spring IOC 的了解

**IoC（Inverse of Control:控制反转）** 是一种设计思想，而不是一个具体的技术实现。IoC 的思想就是将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理。不过， IoC 并非 Spring 特有，在其他语言中也有应用。

**为什么叫控制反转？**

- **控制** ：指的是对象创建（实例化、管理）的权力
- **反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141054462.png)

将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。 IoC 容器就像是一个工厂一样，当我们需要创建一个对象的时候，只需要配置好配置文件/注解即可，完全不用考虑对象是如何被创建出来的。

在实际项目中一个 Service 类可能依赖了很多其他的类，假如我们需要实例化这个 Service，你可能要每次都要搞清这个 Service 所有底层类的构造函数，这可能会把人逼疯。如果利用 IoC 的话，你只需要配置好，然后在需要的地方引用就行了，这大大增加了项目的可维护性且降低了开发难度。

在 Spring 中， IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value），Map 中存放的是各种对象。

Spring 时代我们一般通过 XML 文件来配置 Bean，后来开发人员觉得 XML 文件来配置不太好，于是 SpringBoot 注解配置就慢慢开始流行起来。

### IOC容器启动流程

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171310656.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171311724.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171311253.png)

### 什么是 Spring Bean？

简单来说，Bean 代指的就是那些被 IoC 容器所管理的对象。

我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是通过配置元数据来定义的。配置元数据可以是 XML 文件、注解或者 Java 配置类。

```xml
<!-- Constructor-arg with 'value' attribute -->
<bean id="..." class="...">
   <constructor-arg value="..."/>
</bean>
```

下图简单地展示了 IoC 容器如何使用配置元数据来管理对象。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141056861.png)

org.springframework.beans和 org.springframework.context 这两个包是 IoC 实现的基础，如果想要研究 IoC 相关的源码的话，可以去看看

### 将一个类声明为 Bean 的注解有哪些?

- @Component ：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。
- @Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- @Controller : 对应 Spring MVC 控制层，主要用户接受用户请求并调用 Service 层返回数据给前端页面。

### @Component 和 @Bean 的区别是什么？

- @Component 注解作用于类，而@Bean注解作用于方法。
- @Component通常是通过类路径扫描来自动侦测以及自动装配到 Spring 容器中（我们可以使用 @ComponentScan 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。@Bean 注解通常是我们在标有该注解的方法中定义产生这个 bean,@Bean告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。
- @Bean 注解比 @Component 注解的自定义性更强，而且很多地方我们只能通过 @Bean 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 Spring容器时，则只能通过 @Bean来实现。

@Bean注解使用示例：

```java
@Configuration
public class AppConfig {
    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl();
    }

}
```

上面的代码相当于下面的 xml 配置

```xml
<beans>
    <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```

下面这个例子是通过 @Component 无法实现的。

```java
@Bean
public OneService getService(status) {
    case (status)  {
        when 1:
                return new serviceImpl1();
        when 2:
                return new serviceImpl2();
        when 3:
                return new serviceImpl3();
    }
}
```

### 注入 Bean 的注解有哪些？

Spring 内置的 `@Autowired` 以及 JDK 内置的 `@Resource` 和 `@Inject` 都可以用于注入 Bean。

|Annotaion|Package|Source|
|---|---|---|
|@Autowired|org.springframework.bean.factory|Spring 2.5+|
|@Resource|javax.annotation|Java JSR-250|
|@Inject|javax.inject|Java JSR-330|

@Autowired 和@Resource使用的比较多一些。

### @Autowired 和 @Resource 的区别是什么？

Autowired 属于 Spring 内置的注解，默认的注入方式为byType（根据类型进行匹配），也就是说会优先根据接口类型去匹配并注入 Bean （接口的实现类）。

**这会有什么问题呢？** 当一个接口存在多个实现类的话，byType这种方式就无法正确注入对象了，因为这个时候 Spring 会同时找到多个满足条件的选择，默认情况下它自己不知道选择哪一个。

这种情况下，注入方式会变为 byName（根据名称进行匹配），这个名称通常就是类名（首字母小写）。就比如说下面代码中的 smsService 就是我这里所说的名称，这样应该比较好理解了吧。

```java
// smsService 就是我们上面所说的名称
@Autowired
private SmsService smsService;
```

举个例子，SmsService 接口有两个实现类: SmsServiceImpl1和 SmsServiceImpl2，且它们都已经被 Spring 容器所管理。

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Autowired
private SmsService smsService;

// 正确注入 SmsServiceImpl1 对象对应的 bean
@Autowired
private SmsService smsServiceImpl1;

// 正确注入  SmsServiceImpl1 对象对应的 bean
// smsServiceImpl1 就是我们上面所说的名称
@Autowired
@Qualifier(value = "smsServiceImpl1")
private SmsService smsService;
```

还是建议通过 @Qualifier 注解来显示指定名称而不是依赖变量的名称。

@Resource属于 JDK 提供的注解，默认注入方式为 byName。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为byType。

@Resource 有两个比较重要且日常开发常用的属性：name（名称）、type（类型）。

```java
public @interface Resource {
    String name() default "";
    Class<?> type() default Object.class;
}
```

如果仅指定 name 属性则注入方式为byName，如果仅指定type属性则注入方式为byType，如果同时指定name 和type属性（不建议这么做）则注入方式为byType+byName。

```java
// 报错，byName 和 byType 都无法匹配到 bean
@Resource
private SmsService smsService;

// 正确注入 SmsServiceImpl1 对象对应的 bean
@Resource
private SmsService smsServiceImpl1;

// 正确注入 SmsServiceImpl1 对象对应的 bean（比较推荐这种方式）
@Resource(name = "smsServiceImpl1")
private SmsService smsService;
```

简单总结一下：

- @Autowired 是 Spring 提供的注解，@Resource 是 JDK 提供的注解。
- Autowired 默认的注入方式为byType（根据类型进行匹配），@Resource默认注入方式为 byName（根据名称进行匹配）。
- 当一个接口存在多个实现类的情况下，@Autowired 和@Resource都需要通过名称才能正确匹配到对应的 Bean。Autowired 可以通过 @Qualifier 注解来显示指定名称，@Resource可以通过 name 属性来显示指定名称。

### Bean 的作用域有哪些?

Spring 中 Bean 的作用域通常有下面几种：

- **singleton** : IoC 容器中只有唯一的 bean 实例。Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype** : 每次获取都会创建一个新的 bean 实例。也就是说，连续 getBean() 两次，得到的是不同的 Bean 实例。
- **request** （仅 Web 应用可用）: 每一次 HTTP 请求都会产生一个新的 bean（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- **session** （仅 Web 应用可用） : 每一次来自新 session 的 HTTP 请求都会产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- **application/global-session** （仅 Web 应用可用）： 每个 Web 应用在启动时创建一个 Bean（应用 Bean），，该 bean 仅在当前应用启动时间内有效。
- **websocket** （仅 Web 应用可用）：每一次 WebSocket 会话产生一个新的 bean。

**如何配置 bean 的作用域呢？**

xml方式

```xml
<bean id="..." class="..." scope="singleton"></bean>
```

注解方式

```java
@Bean
@Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Person personPrototype() {
    return new Person();
}
```

### 单例 Bean 的线程安全问题了解吗？

大部分时候我们并没有在项目中使用多线程，所以很少有人会关注这个问题。单例 Bean 存在线程问题，主要是因为当多个线程操作同一个对象的时候是存在资源竞争的。

常见的有两种解决办法：

1. 在 Bean 中尽量避免定义可变的成员变量。
2. 在类中定义一个 ThreadLocal 成员变量，将需要的可变成员变量保存在 ThreadLocal 中（推荐的一种方式）。

不过，大部分 Bean 实际都是无状态（没有实例变量）的（比如 Dao、Service），这种情况下， Bean 是线程安全的。


### Bean 的生命周期了解么?

- Bean 容器找到配置文件中 Spring Bean 的定义。
- Bean 容器利用 Java Reflection API 创建一个 Bean 的实例。
- 如果涉及到一些属性值 利用 set()方法设置一些属性值。
- 如果 Bean 实现了 BeanNameAware 接口，调用 setBeanName()方法，传入 Bean 的名字。
- 如果 Bean 实现了 BeanClassLoaderAware 接口，调用 setBeanClassLoader()方法，传入 ClassLoader对象的实例。
- 如果 Bean 实现了 BeanFactoryAware 接口，调用 setBeanFactory()方法，传入 BeanFactory对象的实例。
- 与上面的类似，如果实现了其他 *.Aware接口，就调用相应的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessBeforeInitialization() 方法
- 如果 Bean 实现了InitializingBean接口，执行afterPropertiesSet()方法。
- 如果 Bean 在配置文件中的定义包含 init-method 属性，执行指定的方法。
- 如果有和加载这个 Bean 的 Spring 容器相关的 BeanPostProcessor 对象，执行postProcessAfterInitialization() 方法
- 当要销毁 Bean 的时候，如果 Bean 实现了 DisposableBean 接口，执行 destroy() 方法。
- 当要销毁 Bean 的时候，如果 Bean 在配置文件中的定义包含 destroy-method 属性，执行指定的方法。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141107712.png)

### Bean循环依赖

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171312607.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171312858.png)

spring默认是不让循环依赖的，但也可以增加配置，让spring解决循环依赖的问题。这里的核心就是了解掌握spring是如何解决循环依赖的。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171313094.png)

增加这个配置，就解决了，AB互相依赖的问题。
先说结论，DefaultSingletonBeanRegistry是解决循环依赖的核心。**核心就是三个缓存区**。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171313983.png)
第三个缓存器其实也是为了解决动态代理的问题，不然两层缓存就可以了。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171314890.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171314672.png)

## Spring AOP

### 对Spring AOP 的了解

AOP(Aspect-Oriented Programming:面向切面编程)能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。

Spring AOP 就是基于动态代理的，如果要代理的对象，实现了某个接口，那么 Spring AOP 会使用 **JDK Proxy**，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代理了，这时候 Spring AOP 会使用 **Cglib** 生成一个被代理对象的子类来作为代理，如下图所示：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141510921.png)

当然你也可以使用 **AspectJ** ！Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。

AOP 切面编程设计到的一些专业术语

|术语|含义|
|---|---|
|目标(Target)|被通知的对象|
|代理(Proxy)|向目标对象应用通知之后创建的代理对象|
|连接点(JoinPoint)|目标对象的所属类中，定义的所有方法均为连接点|
|切入点(Pointcut)|被切面拦截 / 增强的连接点（切入点一定是连接点，连接点不一定是切入点）|
|通知(Advice)|增强的逻辑 / 代码，也即拦截到目标对象的连接点之后要做的事情|
|切面(Aspect)|切入点(Pointcut)+通知(Advice)|
|Weaving(织入)|将通知应用到目标对象，进而生成代理对象的过程动作|

### Spring AOP 和 AspectJ AOP 的区别

**Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单，

如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多。

### AspectJ 定义的通知类型有哪些？

- **Before**（前置通知）：目标对象的方法调用之前触发
- **After** （后置通知）：目标对象的方法调用之后触发
- **AfterReturning**（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- **AfterThrowing**（异常通知） ：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- **Around**： （环绕通知）编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法。

### 多个切面的执行顺序如何控制？

1、通常使用@Order 注解直接定义切面顺序

```java
// 值越小优先级越高
@Order(3)
@Component
@Aspect
public class LoggingAspect implements Ordered {

```

**2、实现Ordered 接口重写 getOrder 方法。**

```java
@Component
@Aspect
public class LoggingAspect implements Ordered {

    // ....

    @Override
    public int getOrder() {
        // 返回值越小优先级越高
        return 1;
    }
}
```


## Spring MVC

### 对 Spring MVC的了解

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141512947.png)

**Spring MVC 时代**

随着 Spring 轻量级开发框架的流行，Spring 生态圈出现了 Spring MVC 框架， Spring MVC 是当前最优秀的 MVC 框架。相比于 Struts2 ， Spring MVC 使用更加简单和方便，开发效率更高，并且 Spring MVC 运行速度更快。

MVC 是一种设计模式，Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行更简洁的 Web 层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)。

### **Spring MVC 的核心组件有哪些？**

记住了下面这些组件，也就记住了 SpringMVC 的工作原理。

- **DispatcherServlet** ：**核心的中央处理器**，负责接收请求、分发，并给予客户端响应。
- **HandlerMapping** ：**处理器映射器**，根据 uri 去匹配查找能处理的 Handler ，并会将请求涉及到的拦截器和 Handler 一起封装。
- **HandlerAdapter** ：**处理器适配器**，根据 HandlerMapping 找到的 Handler ，适配执行对应的 Handler；
- **Handler** ：**请求处理器**，处理实际请求的处理器。
- **ViewResolver** ：**视图解析器**，根据 Handler 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 DispatcherServlet 响应客户端

### **SpringMVC 工作原理了解吗?**

**Spring MVC 原理如下图所示：**

> SpringMVC 工作原理的图解我没有自己画，直接图省事在网上找了一个非常清晰直观的，原出处不明。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141513812.png)

**流程说明（重要）：**

1. 客户端（浏览器）发送请求， DispatcherServlet拦截请求。
2. DispatcherServlet 根据请求信息调用 HandlerMapping 。HandlerMapping 根据 uri 去匹配查找能处理的 Handler（也就是我们平常说的 Controller 控制器） ，并会将请求涉及到的拦截器和 Handler 一起封装。
3. DispatcherServlet 调用 HandlerAdapter适配执行 Handler 。
4. Handler 完成对用户请求的处理后，会返回一个 ModelAndView 对象给DispatcherServlet，ModelAndView 顾名思义，包含了数据模型以及相应的视图的信息。Model 是返回的数据对象，View 是个逻辑上的 View。
5. ViewResolver 会根据逻辑 View 查找实际的 View。
6. DispaterServlet 把返回的 Model 传给 View（视图渲染）。
7. 把 View 返回给请求者（浏览器）


### **统一异常处理怎么做？**

推荐使用注解的方式统一异常处理，具体会使用到 @ControllerAdvice + @ExceptionHandler 这两个注解 。

```java
@ControllerAdvice
@ResponseBody
public class GlobalExceptionHandler {

    @ExceptionHandler(BaseException.class)
    public ResponseEntity<?> handleAppException(BaseException ex, HttpServletRequest request) {
      //......
    }

    @ExceptionHandler(value = ResourceNotFoundException.class)
    public ResponseEntity<ErrorReponse> handleResourceNotFoundException(ResourceNotFoundException ex, HttpServletRequest request) {
      //......
    }
}

```

这种异常处理方式下，会给所有或者指定的 Controller 织入异常处理的逻辑（AOP），当 Controller 中的方法抛出异常的时候，由被@ExceptionHandler 注解修饰的方法进行处理。

ExceptionHandlerMethodResolver 中 getMappedMethod 方法决定了异常具体被哪个被 @ExceptionHandler 注解修饰的方法处理异常。

```java
@Nullable
 private Method getMappedMethod(Class<? extends Throwable> exceptionType) {
  List<Class<? extends Throwable>> matches = new ArrayList<>();
    //找到可以处理的所有异常信息。mappedMethods 中存放了异常和处理异常的方法的对应关系
  for (Class<? extends Throwable> mappedException : this.mappedMethods.keySet()) {
   if (mappedException.isAssignableFrom(exceptionType)) {
    matches.add(mappedException);
   }
  }
    // 不为空说明有方法处理异常
  if (!matches.isEmpty()) {
      // 按照匹配程度从小到大排序
   matches.sort(new ExceptionDepthComparator(exceptionType));
      // 返回处理异常的方法
   return this.mappedMethods.get(matches.get(0));
  }
  else {
   return null;
  }
 }

```

从源代码看出：getMappedMethod()会首先找到可以匹配处理异常的所有方法信息，然后对其进行从小到大的排序，最后取最小的那一个匹配的方法(即匹配度最高的那个)。


## SpringBoot
### SpringBoot自动配置原理

Spring Boot 的核心理念是约定优于配置（Convention over Configuration），旨在通过提供一系列默认配置来简化应用程序的开发和部署。其中最重要的一个特性是自动配置（Auto-configuration）。

Spring Boot 的自动配置是基于 Spring 的条件化配置（Conditional Configuration）功能实现的。Spring Boot 会根据应用程序的依赖关系和其他一些因素来决定需要自动配置哪些组件，以及如何配置这些组件。

@EnableAutoConfiguration是Spring Boot自动配置的核心，其实现原理主要包括以下几个步骤：

Spring Boot在启动时，会扫描classpath下的META-INF/spring.factories文件，并解析其中的EnableAutoConfiguration实现类。

Spring Boot将自动配置实现类加载到容器中，并对自动配置进行条件判断。条件不满足的自动配置将被过滤掉，只有满足条件的自动配置才会生效。

Spring Boot会根据当前应用所依赖的jar包，自动配置需要的Bean，如DataSource、JdbcTemplate等。

Spring Boot还提供了一些默认的配置，如端口号、上下文路径等，这些配置也是通过自动配置实现的。

总的来说，Spring Boot自动配置的原理就是利用@EnableAutoConfiguration和条件注解实现了对Bean的自动配置，并且提供了默认的配置，可以帮助开发者快速搭建一个基于Spring Boot的Web应用程序。

### Spring Boot的核心注解是哪些

Spring Boot 的核心注解主要包括：

@SpringBootApplication：一个复合注解，包括了@Configuration、@EnableAutoConfiguration 和 @ComponentScan 三个注解，用于标识 Spring Boot 应用的主类。

@Configuration：表明该类是一个 Java 配置类，用于定义 Spring Bean 的配置信息。

@EnableAutoConfiguration：自动配置注解，用于开启 Spring Boot 的自动配置功能。

@ComponentScan：用于自动扫描和注册 Spring Bean。

除此之外，还有一些常用的注解，如：

@RestController：用于定义 RESTful 接口，相当于 @Controller 和 @ResponseBody 的组合。

@RequestMapping：用于定义 HTTP 请求的映射，用于处理 HTTP 请求。

@Autowired：自动注入注解，用于自动装配 Spring Bean。

@Value：属性注入注解，用于注入配置文件中的属性值。

这些注解共同组成了 Spring Boot 应用的核心框架。

### SpringBoot的核心配置文件有哪几个

Spring Boot的核心配置文件有以下几个：

application.properties

application.yml

bootstrap.properties

bootstrap.yml

其中，application.properties和application.yml是应用程序的配置文件，而bootstrap.properties和bootstrap.yml是用于Spring Boot上下文的配置文件。它们之间的区别在于加载的优先级不同，bootstrap配置文件的优先级更高，可以用于设置一些比较敏感和重要的配置信息。

application.properties是以键值对的形式进行配置的，例如

```xml
    server.port=8080
    spring.datasource.url=jdbc:mysql://localhost:3306/test
```

application.yml则采用缩进的方式进行配置，例如：

```java
server:
	port: 8080
spring:
	datasource:
		url: jdbc:mysql://localhost:3306/test

```

在Spring Boot应用程序中，我们可以使用这些配置文件来配置应用程序、数据库、日志等方面的配置信息，简化了应用程序的配置过程。在Spring Boot中，这些配置文件的属性可以通过注解@Value注入到Java类中，并且可以通过@ConfigurationProperties注解来将一组属性绑定到一个类中。

### 什么是Spring Boot Starter？有哪些常用的

Spring Boot Starter是一种特殊的依赖关系，它是为了方便Spring Boot应用程序的快速启动和集成而设计的。一个Starter通常会包含多个相关的依赖库，以及自动配置和其他必要的配置文件，使得我们只需引入一个Starter依赖，即可快速地集成相关的技术或库。

常用的Spring Boot Starter包括：

spring-boot-starter-web：适用于开发Web应用程序的Starter，包含Spring MVC、Tomcat和Spring Boot Web自动配置等。

spring-boot-starter-data-jpa：适用于使用JPA的Starter，包含Hibernate、Spring Data JPA、Spring ORM等依赖。

spring-boot-starter-data-redis：适用于使用Redis的Starter，包含Jedis客户端和Spring Data Redis等依赖。

spring-boot-starter-test：适用于测试的Starter，包含JUnit、Mockito、Hamcrest、Spring Test等依赖。

spring-boot-starter-actuator：适用于监控和管理Spring Boot应用程序的Starter，包含Spring Boot Actuator和Spring Boot Web等依赖。

通过使用Spring Boot Starter，我们可以非常方便地集成常用的技术和库，大大提高了开发效率和便捷性。


### SpringBoot 打成jar和普通的jar有什么区别

Spring Boot应用打成的jar和普通的jar包在打包的方式和使用方式上有一些区别。

首先，Spring Boot打成的jar包是一种可执行的jar包，包含了内嵌的Tomcat、Jetty等Web服务器，可以直接运行。而普通的jar包只包含类、资源等，需要在外部环境中手动配置Web服务器才能运行。

其次，Spring Boot打成的jar包中包含了启动类的main方法，可以直接运行该jar包来启动应用程序。而普通的jar包中没有默认的main方法，需要手动指定启动类来运行应用程序。

此外，Spring Boot打成的jar包还包含了一些默认的配置文件，例如application.properties或application.yml，这些文件可以用于配置应用程序的属性、数据源等信息。而普通的jar包不包含这些默认的配置文件。

最后，Spring Boot打成的jar包可以使用spring-boot-maven-plugin插件来打包，该插件可以帮助我们将应用程序的依赖项、配置文件等全部打包进一个可执行的jar包中，方便部署和运行。而普通的jar包需要手动处理依赖项、配置文件等，比较繁琐。


### springboot启动流程

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171309407.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171309936.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171309299.png)

