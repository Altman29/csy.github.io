---
title: 通关微服务-feign
tags:
  - feign
categories:
  - 微服务
keywords: feign
index_img: /img/feign/feign.png
excerpt: 与dubbo同样是一款优秀的RPC远程调用框架。
abbrlink: 2c7fc5c1
date: 2021-07-15 14:21:15
---
>同样是rpc远程调用框架，有dubbo为什么还有再掌握feign呢？
>先说结论：
>Feign 是一个声明式的伪 Http 客户端，它使得写 Http 客户端变得更简单。使用 Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用 Feign 注解和 JAX-RS 注解。Feign 支持可插拔的编码器和解码器。Feign 默认集成了 Ribbon，Nacos 也很好的兼容了 Feign，默认实现了负载均衡的效果。
>虽然dubbo在性能上有优势，**不过这种性能差异除非是达极高的并发量级，否则无需过多考虑。** 相比之下，个人更偏好Feign，因为比较简洁、优雅。

---

# Feign替代RestTemplate

先来看我们以前利用RestTemplate发起远程调用的代码：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190936149.png)

存在下面的问题：

•代码可读性差，编程体验不统一
•参数复杂URL难以维护

其作用就是帮助我们优雅的实现http请求的发送，解决上面提到的问题。

feign使用步骤如下

## 引入依赖

我们在order-service服务的pom文件中引入feign的依赖：

```pom
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

## 添加注解

在order-service的**启动类**添加注解开启Feign的功能：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190937087.png)

## 编写Feign的客户端

在order-service中新建一个接口，内容如下：

```java
package cn.itcast.order.client;

import cn.itcast.order.pojo.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient("userservice")
public interface UserClient {
    @GetMapping("/user/{id}")
    User findById(@PathVariable("id") Long id);
}
```

这个客户端主要是**基于SpringMVC的注解来声明远程调用的信息**，比如：

- 服务名称：userservice
- 请求方式：GET
- 请求路径：/user/{id}
- 请求参数：Long id
- 返回值类型：User

这样，Feign就可以帮助我们发送http请求，无需自己使用RestTemplate来发送了。

## 测试

修改order-service中的OrderService类中的queryOrderById方法，使用Feign客户端代替RestTemplate：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190939525.png)

## 总结

使用Feign的步骤：

① 引入依赖
② 添加@EnableFeignClients注解
③ 编写FeignClient接口
④ 使用FeignClient中定义的方法代替RestTemplate


# 自定义配置

Feign可以支持很多的自定义配置，如下表所示：

|类型|作用|说明|
|---|---|---|
|**feign.Logger.Level**|修改日志级别|包含四种不同的级别：NONE、BASIC、HEADERS、FULL|
|feign.codec.Decoder|响应结果的解析器|http远程调用的结果做解析，例如解析json字符串为java对象|
|feign.codec.Encoder|请求参数编码|将请求参数编码，便于通过http请求发送|
|feign. Contract|支持的注解格式|默认是SpringMVC的注解|
|feign. Retryer|失败重试机制|请求失败的重试机制，默认是没有，不过会使用Ribbon的重试|

一般情况下，默认值就能满足我们使用，如果要自定义时，只需要创建自定义的@Bean覆盖默认Bean即可。

下面以日志为例来演示如何自定义配置。

## 配置文件方式

基于配置文件修改feign的日志级别可以针对单个服务：

```yml
feign:  
  client:
    config: 
      userservice: # 针对某个微服务的配置
        loggerLevel: FULL #  日志级别 
```

也可以针对所有服务：

```yml
feign:  
  client:
    config: 
      default: # 这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
        loggerLevel: FULL #  日志级别 
```

而日志的级别分为四种：

- NONE：不记录任何日志信息，这是默认值。
- BASIC：仅记录请求的方法，URL以及响应状态码和执行时间
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。
## Java代码方式

也可以基于Java代码来修改日志级别，先声明一个类，然后声明一个Logger.Level的对象：

```java
public class DefaultFeignConfiguration  {
    @Bean
    public Logger.Level feignLogLevel(){
        return Logger.Level.BASIC; // 日志级别为BASIC
    }
}
```

如果要**全局生效**，将其放到启动类的@EnableFeignClients这个注解中：

```java
@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration .class) 
```

如果是**局部生效**，则把它放到对应的@FeignClient这个注解中：

```java
@FeignClient(value = "userservice", configuration = DefaultFeignConfiguration .class) 
```

# 使用优化

Feign底层发起http请求，依赖于其它的框架。其底层客户端实现包括：

•URLConnection：默认实现，不支持连接池
•Apache HttpClient ：支持连接池
•OKHttp：支持连接池

因此提高Feign的性能主要手段就是使用**连接池**代替默认的URLConnection。

这里我们用Apache的HttpClient来演示。

## 引入依赖

在order-service的pom文件中引入Apache的HttpClient依赖：

```pom
<!--httpClient的依赖 -->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
</dependency>
```

## 配置连接池

在order-service的application.yml中添加配置：

```yml
feign:
  client:
    config:
      default: # default全局的配置
        loggerLevel: BASIC # 日志级别，BASIC就是基本的请求和响应信息
  httpclient:
    enabled: true # 开启feign对HttpClient的支持!!!
    max-connections: 200 # 最大的连接数
    max-connections-per-route: 50 # 每个路径的最大连接数
```

接下来，在FeignClientFactoryBean中的loadBalance方法中打断点：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190943107.png)

Debug方式启动order-service服务，可以看到这里的client，底层就是Apache HttpClient：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190944530.png)

总结，Feign的优化：

1.日志级别尽量用basic
2.使用HttpClient或OKHttp代替URLConnection
① 引入feign-httpClient依赖
② 配置文件开启httpClient功能，设置连接池参数

# 最佳实践

所谓最近实践，就是使用过程中总结的经验，最好的一种使用方式。

观察可以发现，Feign的客户端与服务提供者的controller代码非常相似：

feign客户端：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190944874.png)

UserController：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190945882.png)

有没有一种办法简化这种重复的代码编写呢？

## 继承方式

一样的代码可以通过继承来共享：

1）定义一个API接口，利用定义方法，并基于SpringMVC注解做声明。
2）Feign客户端和Controller都集成改接口

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190945915.png)

优点：

- 简单
- 实现了代码共享

缺点：

- 服务提供方、服务消费方紧耦合
- 参数列表中的注解映射并不会继承，因此Controller中必须再次声明方法、参数列表、注解

## 抽取方式

将Feign的Client抽取为独立模块，并且把接口有关的POJO、默认的Feign配置都放到这个模块中，提供给所有消费者使用。

例如，将UserClient、User、Feign的默认配置都抽取到一个feign-api包中，所有微服务引用该依赖包，即可直接使用。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190946725.png)

## 基于抽取方式的最佳实践

### 抽取

首先创建一个module，命名为feign-api：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190948682.png)

项目结构：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190948827.png)

在feign-api中然后引入feign的starter依赖：

```pom
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

然后，order-service中编写的UserClient、User、DefaultFeignConfiguration都复制到feign-api项目中：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190949199.png)

### 使用feign-api

首先，删除order-service中的UserClient、User、DefaultFeignConfiguration等类或接口。

在order-service的pom文件中中引入feign-api的依赖：

```pom
<dependency>
    <groupId>cn.itcast.demo</groupId>
    <artifactId>feign-api</artifactId>
    <version>1.0</version>
</dependency>
```

修改order-service中的所有与上述三个组件有关的导包部分，改成导入feign-api中的包

### 测试

重启后，发现服务报错了：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403190950020.png)

这是因为UserClient现在在cn.itcast.feign.clients包下，

而order-service的@EnableFeignClients注解是在cn.itcast.order包下，不在同一个包，无法扫描到UserClient。

### 解决扫描包问题

**方式一**

指定Feign应该扫描的包：

```java
@EnableFeignClients(basePackages = "cn.itcast.feign.clients")
```

**方式二**

指定需要加载的Client接口：

```java
@EnableFeignClients(clients = {UserClient.class})
```

这样就完成了Feign的最佳实践。

# Feign与Dubbo

**Feign**是Spring Cloud提供的一个声明式的伪Http客户端，它使得调用远程服务就像调用本地服务一样简单，只需要创建一个接口并添加一个注解即可。

Nacos注册中心很好的兼容了Feign，Feign默认集成了Ribbon，所以在Nacos下使用Fegin默认就实现了负载均衡的效果。

**Dubbo**是阿里巴巴开源的基于Java的高性能RPC分布式服务框架，致力于提供高性能和透明化的RPC远程服务调用方案，以及SOA服务治理方案。

Spring-cloud-alibaba-dubbo是基于SpringCloudAlibaba技术栈对dubbo技术的一种封装，目的在于实现基于RPC的服务调用。

## 对比

### 协议支持方面

Feign更加优雅简单。Feign是通过REST API实现的远程调用，基于Http传输协议，服务提供者需要对外暴露Http接口供消费者调用，服务粒度是http接口级的。通过短连接的方式进行通信，不适合高并发的访问。Feign追求的是简洁，少侵入（因为就服务端而言，在SpringCloud环境下，不需要做任何额外的操作，而Dubbo的服务端需要配置开放的Dubbo接口)。

Dubbo方式更灵活。Dubbo是通过RPC调用实现的远程调用，支持多传输协议(Dubbo、Rmi、http、redis等等)，可以根据业务场景选择最佳的方式，非常灵活。默认的Dubbo协议：利用Netty，TCP传输，单一、异步、长连接，适合数据量小、高并发和服务提供者远远少于消费者的场景。Dubbo通过TCP长连接的方式进行通信，服务粒度是方法级的。

从协议层选择看，Dubbo是配置化的，更加灵活。Dubbo协议更适合小数据高并发场景。

### 通信性能方面

从通信的性能上来分析，SpringCloud的通信采用Openfeign（feign）组件。

Feign基于Http传输协议，底层实现是rest。从OSI 7层模型上来看rest属于应用层。

在高并发场景下性能不够理想，成为性能瓶颈（虽然他是基于Ribbon以及带有熔断机制可以防止雪崩），需要改造。具体需要改造的内容需要时再研究。

Dubbo框架的通信协议采用RPC协议，属于传输层协议，性能上自然比rest高。提升了交互的性能，保持了长连接，高性能。

Dubbo性能更好，比如支持异步调用、Netty性能更好。Dubbo主要是配置而无需改造。

|RPC|REST|
|---|---|
|耦合性|强耦合|松耦合|
|消息协议|二进制 thrift/protobuf|文本 xml、jason|
|通信协议|TCP|HTTP|
|接口契约IDL|thrift/protobuf|swagger|
|开发调试|消息不可读|可读，可调试|
|对外开放|一般作为内部各个系统的通信框架|对接外部系统|


### 负载均衡方面

Feign默认使用Ribbon作为负载均衡的组件。

Dubbo和Ribbon（Feign默认集成Ribbon）都支持负载均衡策略，但是Dubbo支持的更灵活。

Dubbo和Ribbon对比：

Ribbon的负载均衡策略：随机、规则轮询、空闲策略、响应时间策略。

Dubbo的负载均衡策略：Dubbo支持4种算法，随机、权重轮询、最少活跃调用数、一致性Hash策略。而且算法里面引入权重的概念。

Dubbo可以使用路由策略，然后再进行负载均衡。

Dubbo配置的形式不仅支持代码配置，还支持Dubbo控制台灵活动态配置。

Dubbo负载均衡的算法可以精准到某个服务接口的某个方法，而Ribbon的算法是Client级别的。Ribbon需要进行全局配置，个性化配置比较麻烦。

### 容错机制方面

Feign默认使用Hystix作为服务熔断的组件。Hystix提供了服务降级，服务熔断，依赖隔离，监控（Hystrix Dashboard）等功能。Feign是利用熔断机制来实现容错的，与Dubbo处理的方式不一样。

Dubbo支持多种容错策略，FailOver、FailFast、Failsafe、FailBack、Aviailable、Broadcast、Forking策略等，以及Mock。也引入了retry次数，timeout等配置参数。Dubbo自带了失败重试的功能。

### 总结

如果项目对性能要求不是很严格，可以选择使用Feign，它使用起来更方便。

如果需要提高性能，避开基于Http方式的性能瓶颈，可以使用Dubbo。