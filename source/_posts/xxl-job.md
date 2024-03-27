---
title: xxl-job
tags:
  - xxl-job
categories:
  - 微服务
keywords: xxl-job
index_img: /img/micro/xxl.jpg
excerpt: 分布式任务调度平台，用于管理和调度定时任务和分布式任务。它支持任务的动态添加、修改、删除和调度执行，提供了任务执行日志、监控、报警等功能。适用各种场景，如数据处理、定时任务、ETL等，帮助实现任务调度的自动化管理，提高工作效率。
abbrlink: 89b2e006
date: 2022-03-09 17:23:27
---
# 概述

在平时的业务场景中，经常有一些场景需要使用定时任务，比如：

- 时间驱动的场景：某个时间点发送优惠券，发送短信等等。
- 批量处理数据：批量统计上个月的账单，统计上个月销售数据等等。
- 固定频率的场景：每隔5分钟需要执行一次。

所以定时任务在平时开发中并不少见，而且对于现在快速消费的时代，每天都需要发送各种推送，消息都需要依赖定时任务去完成，应用非常广泛。

# 为什么需要任务调度平台

在Java中，传统的定时任务实现方案，比如Timer，Quartz等都或多或少存在一些问题：

- 不支持集群、不支持统计、没有管理平台、没有失败报警、没有监控等等

而且在现在分布式的架构中，有一些场景需要分布式任务调度：

- 同一个服务多个实例的任务存在互斥时，需要统一的调度。
- 任务调度需要支持高可用、监控、故障告警。
- 需要统一管理和追踪各个服务节点任务调度的结果，需要记录保存任务属性信息等。

显然传统的定时任务已经不满足现在的分布式架构，所以需要一个分布式任务调度平台。

# 架构设计

下面简单地说一下xxl-job的架构，我们先看官网提供的一张架构图来分析。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271456253.png)

**总体分两个部分：**

- 调度中心：负责管理调度信息，按照调度配置发出调度请求，自身不承担业务代码。调度系统和任务解耦，提高了系统可用性和稳定性。通调度性能不在受限于任务模块。
- 执行器：负责接收调度中的请求并执行任务逻辑。任务模块专注于任务的执行操作，开发和运维更加简单和高校。

# xxl-job原理

## 执行器原理

执行器启动主要是把自己注册到调度中心然后保存在数据库（xxl_job_registry表），并定时发送心跳，保持续约。执行器正常关闭，也主动告知调度中心注销，这种是主动注册。

如果执行器网络故障，调度中心就不知道执行器的情况，如果把任务路由给一个不可用的执行器，就会导致任务失败。所以调度中心需要不断的对执行器探活（RocketMQ的NameServer 管理broker一样），调度中心会启动一个后台线程定时调用执行器接口，如果发现异常就下线。

## 调度中心和任务执行

- JobRegistryMonitorHelper 不停的更新注册表，把超时的执行器剔除（每隔30s执行一次）
- 创建线程池
	- 调度器线程ScheduleThread：计算预读取的任务数（默认6000），然后while 循环不停的获取到期的任务
	- 时间轮线程池
- 获取任务锁：第一步获取数据库排它锁，如果没有成功说明其他的调度中心在加载任务
- 查询任务：获取锁后， 查询任务
- 调度任务
- 任务触发，选择执行器：按照配置的路由策略，不通路由策略获取方式也不一样
- 远程执行：拿到执行器之后，runExecutor 触发远程的执行器
- 执行器处理远程调用，回调

### 时间轮

一批任务都是不同的时间执行，执行时间精确到秒，如何实现对所有的任务调度这个就是**时间轮**。

### 任务超时

如果任务在指定的时间范围内没有返回结果，就不在等结果，抛出异常。

### 失败重试

如果任务执行失败，会更新在xxl_job_log日志表里。调度中心有个后台线程monitorThread。第一步就是查日志表里结果不是200的任务，为了防止集群下同时处理一个失败任务，用了数据库的乐观锁（版本号），如果失败重试次数>0，代表重试，就要重新触发。

### 故障转移

如果一个执行器挂了，就找另一个执行器执行，直到找到一个正常的执行器。

### 任务数据分片

这里的是数据分片，需要用到分片参数 **sharding param**，调度器负责把这个分片参数分发给每个执行器（**执行器个数和参数个数相等**），怎么根据分片参数对数据分片是Job自己的事情。


# 基本使用

## 准备

源码仓库地址：[https://github.com/xuxueli/xxl-job](https://github.com/xuxueli/xxl-job)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271503084.png)

**xxl-job-admin: 调度中心,统一管理任务调度平台上的调度任务，负责触发调度执行，并且提供任务管理平台。**
**xxl-job-core: 接收调度中心的调度并且执行,公共的jar包。**
**xxl-job-executor-samples: 服务端,这里展示的是xxl-job的演示案例**

执行xxl-job准备好的sql

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271504461.png)

修改admin模块中的数据库连接,并启动该模块,也可以达成jar包部署

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271504628.png)

[http://localhost:8080/xxl-job-admin](http://localhost:8080/xxl-job-admin) 默认账号密码:admin/123456

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271505208.png)

## 服务端配置


引入xxl-job的starter

```pom
<dependency>
    <groupId>com.xuxueli</groupId>
    <artifactId>xxl-job-core</artifactId>
    <version>${版本}</version>
</dependency>
```

配置文件

```yml
xxl:
  job:
    # 执行器开关
    enabled: true
    # 调度中心地址：如调度中心集群部署存在多个地址则用逗号分隔。
    admin-addresses: http://localhost:8080/xxl-job-admin
    # 执行器通讯TOKEN：非空时启用
    access-token: default_token
    # 执行器配置
    executor:
      # 执行器AppName：执行器心跳注册分组依据；为空则关闭自动注册
      appname: demo-executor
      # 执行器端口号 执行器从19901开始往后写
      port: 9901
      # 执行器注册：默认IP:PORT
      address:
      # 执行器IP：默认自动获取IP
      ip:
      # 执行器运行日志文件存储磁盘路径
      logpath: ./logs/${spring.application.name}/xxl-job
      # 执行器日志文件保存天数：大于3生效
      logretentiondays: 30

```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271506038.png)

**客户端需要配置的执行器就是这里的`executor`,页面的`AppName`对应的就是`demo-executor`,名称随意填写**

**注册方式如果选择自动注入,服务端又没有配置的话就会自动读取服务端的地址,手动录入就是手动输入服务端的地址,就是`executor`中配置的ip和port**

这里的`access-token`执行器通讯TOKEN,非空时启用,启动的话需要跟admin模块application.yml的配置文件中的token保持一致

## 注入bean

```java
@Data
@ConfigurationProperties(prefix = "xxl.job")
public class XxlJobProperties {

    private Boolean enabled;

    private String adminAddresses;

    private String accessToken;

    private Executor executor;

    @Data
    @NoArgsConstructor
    public static class Executor {

        private String appname;

        private String address;

        private String ip;

        private int port;

        private String logPath;

        private int logRetentionDays;
    }
}

```

```java
@EnableConfigurationProperties(XxlJobProperties.class)
@AutoConfiguration
@AllArgsConstructor
@ConditionalOnProperty(prefix = "xxl.job", name = "enabled", havingValue = "true")
@Slf4j
public class XxlJobConfig {

    private final XxlJobProperties xxlJobProperties;

    @Bean
    public XxlJobSpringExecutor xxlJobExecutor() {
        log.info(">>>>>>>>>>> xxl-job config init.");
        XxlJobSpringExecutor xxlJobSpringExecutor = new XxlJobSpringExecutor();
        xxlJobSpringExecutor.setAdminAddresses(xxlJobProperties.getAdminAddresses());
        xxlJobSpringExecutor.setAccessToken(xxlJobProperties.getAccessToken());
        XxlJobProperties.Executor executor = xxlJobProperties.getExecutor();
        xxlJobSpringExecutor.setAddress(executor.getAddress());
        xxlJobSpringExecutor.setIp(executor.getIp());
        xxlJobSpringExecutor.setPort(executor.getPort());
        xxlJobSpringExecutor.setLogPath(executor.getLogPath());
        xxlJobSpringExecutor.setLogRetentionDays(executor.getLogRetentionDays());
        return xxlJobSpringExecutor;
    }
}

```

## 启动

这里就使用最简单的使用注解的方式来执行定时任务

定时任务的业务逻辑

```java
@Service
@Slf4j
public class JobService {

    @XxlJob(value = "demoJobHandler", init = "init", destroy = "destroy")
    public void demoJobHandler2() throws Exception {
        System.out.println("欢迎来到定时任务");
        XxlJobHelper.log("XXL-JOB, Hello World.");
        for (int i = 0; i < 5; i++) {
            XxlJobHelper.log("beat at:" + i);
        }
        // default success
    }

    // 初始化执行
    public void init() {
        log.info("init");
    }

    // 销毁执行
    public void destroy() {
        log.info("destory");
    }

}

```

新增任务

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271508787.png)

```
- 执行器: 选择刚刚自定义好的执行器
- 调度类型: 选择定时任务的执行方式
- Cron: 定时任务的执行周期
- 运行模式: 如果我们使用的是注解的方式,就选择BEAN类型
- JobHandler: 就设置成服务端@XxlJob(value = "demoJobHandler")这里面的值
```

执行

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403271509117.png)

执行一次就只执行一次,启动就是根据设置的执行周期开始定时任务。

更加详细的使用可见官方文档: [https://www.xuxueli.com/xxl-job/](https://www.xuxueli.com/xxl-job/)