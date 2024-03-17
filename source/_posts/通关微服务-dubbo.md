---
title: 通关微服务-dubbo
tags:
  - dubbo
categories:
  - 微服务
index_img: /img/micro/dubbo.png
excerpt: 它提供了服务治理、负载均衡、远程通信和可扩展性等功能，使开发者能够轻松创建和管理分布式应用。具有强大的生态系统，被广泛用于构建微服务和大规模分布式系统。
abbrlink: 198ade24
date: 2021-07-09 20:21:15
---
# 分布式系统中相关概念
## 大型互联网项目架构特点

- 用户多
- 流量大，并发高
- 海量数据
- 易受攻击
- 功能繁琐
- 变更快

## 大型互联网项目架构目标

- 高性能：提供快速的访问体验
- 高可用：网站服务一直可以正常访问
- 可伸缩：通过硬件增加/减少，提高/降低处理能力
- 高可扩展：系统间耦合低，方便通过新增/移除方式，增加/减少功能/模块
- 安全性：提供网站安全访问和数据加密，安全存储等策略
- 敏捷性：随需应变，快速响应

## 集群和分布式

- 集群：很多"人"一起，干一样的事
	- 一个业务模块，部署在多台服务器上。
- 分布式：很多"人"一起，干不一样的事。这些不一样的事，合起来是一件大事
	- 一个大的业务系统，拆分为小的业务模块，分别部署在不同机器上。

（eg.饭店，厨师，洗菜、切菜、炒菜）

## 架构演进

### 单体架构

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171445668.png)
优点：
- 简单：开发部署都很方便，小型项目首选

缺点：
- 项目启动慢
- 可靠性差
- 可伸缩性差
- 扩展性和可维护性差
- 性能低

### 垂直架构

在某个领域细分就是垂直的意思。
垂直架构是指单体架构中的多个模块拆分为多个独立的项目。行程多个独立的单体架构。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171449282.png)

垂直架构存在的问题：
- 重复功能多


### 分布式架构

分布式架构是指在垂直架构的基础上，将公共业务模块抽取出来，作为独立的服务，供其他调用者消费，以实现服务的共享和重用。

RPC：Remote Procedure Call远程过程调用。有非常多的协议和技术都来实现了RPC过程。比如：HTTP REST风格，Java RMI规范等等。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171453372.png)

分布式架构存在的问题：
- 服务的提供方一旦产生变更，所有消费方都需要变更

### SOA架构

![[Pasted image 20240317145745.png]]

SOA：Service-Oriented Architecture，面向服务的架构。是一个组件模型，它将应用程序的不同功能单元（称为服务）进行拆分，并通过这些服务之间定义良好的接口和契约联系起来。

ESB：Enterparise Service Bus企业服务总线，服务中介。主要提供了一个服务于服务之间的交互。ESB包含的功能包含：负载均衡，流量控制，加密处理，服务的监控，异常处理，监控告急等等。

### 微服务架构

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171502998.png)

微服务架构是在SOA上做的升华，微服务架构强调的一个重点是"业务需要彻底的组件化和服务化"，原有的单个业务系统会拆分为多个可以独立开发、设计、运行的小应用。这些小应用之间通过服务完成交互和集成。

微服务架构 = 80%SOA服务架构思想 + 100%的组件化思想 + 80%的领域建模思想。

特点：
- 服务实现组件化：开发者可以自由选择开发技术，也不需要协调其他团队
- 服务之间交互一般采用REST API
- 去中心化：每个微服务有自己私有的数据库持久化业务数据
- 自动化部署：把应用拆分成为一个一个独立的单个服务，方便自动化部署、测试、运维

### 总结

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171504101.png)

<font color="#ffc000">注：</font> Dubbo是SOA时代的产物，SpringCloud是微服务时代的产物。


# Dubbo概述

## Dubbo概念

- Dubbo是阿里开源的一个高性能、轻量级的Java RPC框架，现已收录到apache了。
- 致力于提供高性能和透明化的RPC<font color="#0070c0">**远程调用方案**</font>，已经SOA服务治理方案。
- [dubbo官网](https://cn.dubbo.apache.org/zh-cn/)
## Dubbo架构

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171511719.png)
编号标注了使用流程，很清晰
0.start：服务的提供者需要运行在容器里，比如tomcat里。
1.register：服务启动后，会注册到注册中心，相当于把服务的IP、端口等信息放到注册中心上。
2.subscribe：消费者想要调用服务，就需要订阅，向注册中心要，把服务提供者的IP、端口地址给我。
3.notify：消费者向注册中心要，注册中心提供信息。
4.invoke：这个时候消费者就拿到了目标服务的信息，就可以调用了，就是RPC的过程。这个过程不用管，这是dubbo内部自动实现。
5.monitor：管理者，是做服务监控的，比如某个服务调用了多少次。


# Dubbo快速入门

## Zookeeper安装

其实zookeeper就是dubbo推荐使用的注册中心。

## Dubbo快速入门

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171559190.png)

### 启动注册中心

对于一个微服务化的应用来说，注册中心是不可或缺的一个组件。只有通过注册中心，消费端才可以成功发现服务端的地址信息，进而进行调用。通过官方教程获取一个基于 Apache Zookeeper 注册中心的简易启动器。

```bash
Windows:
git clone --depth=1 --branch master git@github.com:apache/dubbo-samples.git
cd dubbo-samples
./mvnw.cmd clean compile exec:java -pl tools/embedded-zookeeper

Linux / MacOS:
git clone --depth=1 --branch master git@github.com:apache/dubbo-samples.git
cd dubbo-samples
./mvnw clean compile exec:java -pl tools/embedded-zookeeper

Docker:
docker run --name some-zookeeper -p 2181:2181 --restart always -d zookeeper
```

### 初始化项目

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171606809.png)

如上图所示，可以建立一个基础的项目。

搭建了基础项目之后，我们还需要创建 `dubbo-spring-boot-demo-interface` 、`dubbo-spring-boot-demo-provider` 和 `dubbo-spring-boot-demo-consumer` 三个子模块。

创建了三个子模块之后，需要创建一下几个文件夹：
1. 在 `dubbo-spring-boot-demo-consumer/src/main/java` 下创建 `org.apache.dubbo.springboot.demo.consumer` package
2. 在 `dubbo-spring-boot-demo-interface/src/main/java` 下创建 `org.apache.dubbo.springboot.demo` package
3. 在 `dubbo-spring-boot-demo-provider/src/main/java` 下创建 `org.apache.dubbo.springboot.demo.provider` package

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171607781.png)

### 添加Maven依赖

在初始化完项目以后，我们需要先添加 Dubbo 相关的 maven 依赖。

对于多模块项目，首先需要在父项目的 `pom.xml` 里面配置依赖信息。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171608057.png)

编辑 `./pom.xml` 这个文件，添加下列配置。

```xml
<properties>
        <dubbo.version>3.2.0-beta.4</dubbo.version>
        <spring-boot.version>2.7.8</spring-boot.version>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencyManagement>
        <dependencies>
            <!-- Spring Boot -->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <!-- Dubbo -->
            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo-bom</artifactId>
                <version>${dubbo.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo-dependencies-zookeeper-curator5</artifactId>
                <version>${dubbo.version}</version>
                <type>pom</type>
            </dependency>
        </dependencies>
    </dependencyManagement>


    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring-boot.version}</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>
```

然后在 `dubbo-spring-boot-consumer` 和 `dubbo-spring-boot-provider` 两个模块 `pom.xml` 中进行具体依赖的配置。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171609745.png)

编辑 `./dubbo-spring-boot-consumer/pom.xml` 和 `./dubbo-spring-boot-provider/pom.xml` 这两文件，都添加下列配置。

```xml
<dependencies>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-samples-spring-boot-interface</artifactId>
            <version>${project.parent.version}</version>
        </dependency>

        <!-- dubbo -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-dependencies-zookeeper-curator5</artifactId>
            <type>pom</type>
            <exclusions>
                <exclusion>
                    <artifactId>slf4j-reload4j</artifactId>
                    <groupId>org.slf4j</groupId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- spring boot starter -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

    </dependencies>
```

在这份配置中，定义了 dubbo 和 zookeeper（以及对应的连接器 curator）的依赖。

添加了上述的配置以后，可以通过 IDEA 的 `Maven - Reload All Maven Projects` 刷新依赖

### 定义服务接口

服务接口 Dubbo 中沟通消费端和服务端的桥梁。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171610622.png)

在 `dubbo-spring-boot-demo-interface` 模块的 `org.apache.dubbo.samples.api` 下建立 `DemoService` 接口，定义如下：

```java
package org.apache.dubbo.springboot.demo;

public interface DemoService {

    String sayHello(String name);
}
```

在 `DemoService` 中，定义了 `sayHello` 这个方法。后续服务端发布的服务，消费端订阅的服务都是围绕着 `DemoService` 接口展开的。

### 定义服务端实现

定义了服务接口之后，可以在服务端这一侧定义对应的实现，这部分的实现相对于消费端来说是远端的实现，本地没有相关的信息。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171611053.png)

在`dubbo-spring-boot-demo-provider` 模块的 `org.apache.dubbo.samples.provider` 下建立 `DemoServiceImpl` 类，定义如下：

```java
package org.apache.dubbo.springboot.demo.provider;

import org.apache.dubbo.config.annotation.DubboService;
import org.apache.dubbo.springboot.demo.DemoService;

@DubboService
public class DemoServiceImpl implements DemoService {

    @Override
    public String sayHello(String name) {
        return "Hello " + name;
    }
}
```

在 `DemoServiceImpl` 中，实现了 `DemoService` 接口，对于 `sayHello` 方法返回 `Hello name`。

注：在`DemoServiceImpl` 类中添加了 `@DubboService` 注解，通过这个配置可以基于 Spring Boot 去发布 Dubbo 服务。

### 配置服务端配置文件

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171612651.png)

在 `dubbo-spring-boot-demo-provider` 模块的 `resources` 资源文件夹下建立 `application.yml` 文件，定义如下：

```yaml
dubbo:
  application:
    name: dubbo-springboot-demo-provider
  protocol:
    name: dubbo
    port: -1
  registry:
    address: zookeeper://${zookeeper.address:127.0.0.1}:2181
```

在这个配置文件中，定义了 Dubbo 的应用名、Dubbo 协议信息、Dubbo 使用的注册中心地址。

### 配置消费端配置文件

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171612573.png)

在 `dubbo-spring-boot-demo-consumer` 模块的 `resources` 资源文件夹下建立 `application.yml` 文件，定义如下：

```yaml
dubbo:
  application:
    name: dubbo-springboot-demo-consumer
  protocol:
    name: dubbo
    port: -1
  registry:
    address: zookeeper://${zookeeper.address:127.0.0.1}:2181
```

在这个配置文件中，定义了 Dubbo 的应用名、Dubbo 协议信息、Dubbo 使用的注册中心地址。

### 配置服务端启动类

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171613225.png)

在 `dubbo-spring-boot-demo-provider` 模块的 `org.apache.dubbo.springboot.demo.provider` 下建立 `Application` 类，定义如下：

```java
package org.apache.dubbo.springboot.demo.provider;

import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubbo
public class ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }
}
```

在这个启动类中，配置了一个 `ProviderApplication` 去读取我们前面第 6 步中定义的 `application.yml` 配置文件并启动应用。

### 配置消费端启动类

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171614281.png)

在 `dubbo-spring-boot-demo-consumer` 模块的 `org.apache.dubbo.springboot.demo.consumer` 下建立 `Application` 类，定义如下：

```java
package org.apache.dubbo.springboot.demo.consumer;

import org.apache.dubbo.config.spring.context.annotation.EnableDubbo;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@EnableDubbo
public class ConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

在这个启动类中，配置了一个 `ConsumerApplication` 去读取我们前面第 7 步中定义的 `application.yml` 配置文件并启动应用。

### 配置消费端请求任务

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171614249.png)

在 `dubbo-spring-boot-demo-consumer` 模块的 `org.apache.dubbo.springboot.demo.consumer` 下建立 `Task` 类，定义如下：

```java
package org.apache.dubbo.springboot.demo.consumer;

import java.util.Date;

import org.apache.dubbo.config.annotation.DubboReference;
import org.apache.dubbo.springboot.demo.DemoService;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class Task implements CommandLineRunner {
    @DubboReference
    private DemoService demoService;

    @Override
    public void run(String... args) throws Exception {
        String result = demoService.sayHello("world");
        System.out.println("Receive result ======> " + result);

        new Thread(()-> {
            while (true) {
                try {
                    Thread.sleep(1000);
                    System.out.println(new Date() + " Receive result ======> " + demoService.sayHello("world"));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                    Thread.currentThread().interrupt();
                }
            }
        }).start();
    }
}
```

在 `Task` 类中，通过`@DubboReference` 从 Dubbo 获取了一个 RPC 订阅，这个 `demoService` 可以像本地调用一样直接调用。在 `run`方法中创建了一个线程进行调用。

### 启动应用

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171615752.png)

首先是启动 `org.apache.dubbo.samples.provider.Application` ，等待一会出现如下图所示的日志（`Current Spring Boot Application is await`）即代表服务提供者启动完毕，标志着该服务提供者可以对外提供服务了。

```fallback
[Dubbo] Current Spring Boot Application is await...
```

然后是启动`org.apache.dubbo.samples.client.Application` ，等待一会出现如下图所示的日志（`Hello world` ）即代表服务消费端启动完毕并调用到服务端成功获取结果。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171615579.png)

```fallback
Receive result ======> Hello world
```

# Dubbo高级特性

## 序列化

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171628734.png)

- dubbo内部已经将序列化和反序列化的过程内部封装了
- 只需要在定义pojo类时**实现Serializable接口**即可
- 一般会定义一个公共的pojo模块，让生产者和消费者都依赖该模块

## 地址缓存

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171638042.png)

## 超时

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171640671.png)

- 服务消费者在调用服务提供者的时候发生了阻塞、等待的情形，服务消费者会一直等待下去。
- 在峰值时刻，大量请求同时到达，会造成线程的大量堆积，会造成服务雪崩。
- dubbo利用超时机制来解决，设置一个超时时间，在时间段内无法完成访问，则自动断开。
- 使用timeout属性配置超时时间，默认值是1000，单位毫秒。

注：推荐配置在服务的提供方。

## 重试

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171648389.png)

- 如果出现了网络抖动，则这一次请求就会失败。
- dubbo提供重试机制来避免类似问题。
- 通过retries属性来设置重试次数，默认是2次。

## 多版本

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171651383.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171651129.png)

<font color="#0070c0">**灰度发布**</font>：当出现新功能时，会让一部分用户先使用新功能，用户反馈没有问题，再将所有用户迁移到新功能。

dubbo中使用version属性来设置和调用同一个接口的不同版本。

## 负载均衡

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171659466.png)


负载均衡策略：
- Random：按权重随机。
- RoundRobin：按权重轮询。
- LeastActive：最少活跃调用，相同活跃是随机。
- ConsistentHash：一致性Hash，相同参数额请求总是发到同一个提供者。

## 集群容错

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171703357.png)

集群容错模式：
- Failover Cluster：失败重试。默认值。当出现失败，重试其他服务器，默认重试2次，使用retries配置，一般用于<font color="#0070c0">读操作</font>。
- Failfast Cluster：快速失败，只发起一次调用，失败立即报错。通常用于<font color="#0070c0">写操作</font>。
- Failsafe Cluster：失败安全，出现异常时，直接忽略。返回一个空结果。
- Failback Cluster：失败自动回复，后台记录失败请求时，定时重发。
- Forking Cluster：并行调用多个服务器，只要一个成功即返回。比较耗费性能。
- Broadcast Cluster：广播调用所有提供者，逐个调用，任意一台报错则报错。同步要求性比较高的情形。

## 服务降级

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403171705376.png)

在B上，同时存在广告服务，日志服务，支付服务。很明显支付服务较为重要，当B机器cpu达到90%时，除了定位异常外，最先考虑的应该是服务降级，要确保支付服务正常，不重要的广告服务、日志服务可以先停掉。这就是服务降级。

dubbo服务降级方式：
- `mock=force:return null` 表示消费方对该服务的方法调用都直接返回null，不发起远程调用。用来屏蔽不重要服务可不用时对调用方的影响。
- `mock=fail:return null` 表示消费方对该服务的方法调用在失败后，再返回null，不抛异常。用来容忍不重要服务不稳定时对调用方的影响。




