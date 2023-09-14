---
title: Spring
excerpt: 是时候重新来一波Spring了，🚗🚗🚗~~~
date: 2023-02-15 15:13:11
categories:
- Spring
tags:
- Spring
cover: /img/spring.jpg
---
<!-- more -->
# Why to learn123?
* 简化开发，降低企业级开发的复杂性;
* 框架整合，高效整合其他技术，提高企业级应用开发与运行效率;

# How to learn?
* 学习spring框架的设计思想;
* 学习如何操作，思考操作与思想的联系;
* 掌握案例熟练应用,体会思想;

# Spring家族
**[官网>>>spring.io<<<](https://spring.io/)**
Spring发展到现在已经形成了一种开发生态圈，提供若干项目，每个项目用于完成特定的功能。
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141650888.png)
其中，`Spring Framework`、`Spring Boot`、`Spring Cloud`是必须掌握的。

# Spring Framework体现架构
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141651643.png)
可以看到官方没有提供5.x新架构图，掌握4.x即可。 </br>
4.x架构如下图(*重要*)，
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141652234.png)

# 核心概念
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141702545.png)
一段业务实现，之前依赖`BookDaoImpl1`，后来业务改变，依赖`BookDaoImpl2`，就需要重新编译，重新打包、测试、上线等等，这是一种**耦合度偏高**的情况。<br>所以，依赖就是写了其他对象的应用么，那不写就可以了，不用主动`new`对象，由外部提供：
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141654541.png)
那么这种思想，就叫做**IoC(Inversion of Control)控制反转，对象的创建控制权由程序转移到外部**。<br>Spring就把这件事给做到了。那么它是怎么做到的呢？Spring内部提供了一个容器，称为IoC容器，用来充当IoC思想中的"外部"。(系统架构图中有一个Core Container，就是它)<br>
那么，现在对象不需要new了，可以直接拿来用么？还是不行，还缺少依赖关系，类与类直接的依赖关系也被Spring搞定了，是通过依赖注入完成的，Bean与Bean之间会维护达到预期的依赖关系。到这，俩个核心思想就都出来了。这个重要思想的核心目的，可以看出来，就是解耦！不用new、不用维护依赖关系。通通交给Spring来完成，或者Spring的IoC容器来完成。
* <font color="red">**IoC(Inversion of Control)控制反转**</font>
  * 使用对象时，由主动new产生对象转换为由外部提供，对象创建控制权转移到外部的思想称为控制反转;
*  Spring技术对IoC思想进行了实现
   *  Spring提供了一个容器，称为IoC容器(Core Container)，用来充当IoC思想中的"外部";
   *  IoC容器负责对象的创建、初始化等一系列工作，被创建或被管理的对象在IoC容器中称为Bean;
*  <font color="red">**DI(Dependency Injection)依赖注入**</font>
   *  在容器中建立Bean与Bean之间的依赖关系的整个过程，称为依赖注入。
   ![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141720317.png)

* **目标: <font color="red">充分解耦</font>**
  * 使用IoC容器管理bean(IoC)
  * 在IoC容器内将有依赖关系的bean进行关系绑定(DI)
* **最终结果**
  * 使用对象时，不仅可以直接从IoC容器中获取，并且获取到的bean已经绑定了所有的依赖关系。

# IoC入门实例
## 分析
+ 管理什么?(Service与Dao)
+ 如何将被管理的对象告知IoC容器?(配置)
+ 被管理的对象交给IoC容器，如何获取到IoC容器?(接口)
+ IoC容器得到后，如何从容器中获取bean?(接口方法)
+ 使用spring需要导入哪些坐标?(pom)
## IoC实例编写
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141834033.png)
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141834482.png)
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141834748.png)
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302141835797.png)
[<center>====传送门====</center>](https://github.com/Altman29/spring_ioc_demo/tree/master)

## now
此时，把bean交给Spring的IoC容器，并且可以从容器获取实例了。那么充分解耦的最终目的达到了么，目前是没有的，因为具体bean里面还存在着`new`来创建对象呢！~ 接下来就看搞定它，下面看下DI入门案例。

# DI入门案例
## 分析
+ 首先，要基于IoC管理bean
+ Service中使用new形式创建的对象是否保留？（否）
+ Service中需要的Dao对象如何进入到Service中？（提供方法）
+ Service与Dao之间的关系如何描述？（配置）

## DI入门实例编写
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/step5.png)
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/step7.png)
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302222308558.png)
[<center>====传送门====</center>](https://github.com/Altman29/spring_ioc_demo/tree/DI_demo)

# bean配置
+ bean的基础配置
+ bean的别名配置
+ bean的作用范围配置

## bean的基础配置
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231517119.png)
## bean的别名
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231558931.png)
## bean的作用范围
多次获取bean，就是多个对象还是一个对象呢？（默认是单例的）
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231559662.png)
如果不想要单例呢，其实，这是可以配置的，只不过默认是单例的。
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231600952.png)
总结如下：
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231602869.png)

+ 为什么bean默认为单例的？
  + 是为了复用！
+ 什么不适合单例呢？
  + 有状态的对象不适合！

# bean的实例化(bean_instance)
## 使用构造方法实例化bean
+ bean本质上就是对象，创建bean使用构造方法完成

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231609926.png)
可以看出来，构造方法私有也不影响对象的创建。可以想到什么？   ---->>>><font color="red">反射</font>
但是，如果给构造方法加一个参数呢？
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231612627.png)
很遗憾,Spring是找不到这个构造的来创建对象的。必须使用<b>无参构造方法</b>！

## 使用静态工厂方式实例化bean
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231624175.png)

## 使用实例工厂方式实例化bean
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231627057.png)

## 使用FactoryBean实例化bean
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231633024.png)

# bean的生命周期(bean_lifecycle)
+ 生命周期：从创建到消亡的完整过程；
+ bean的生命周期：bean从创建到销毁的整体过程；
+ bean的生命周期控制：在bean创建后到销毁前做一些事情；

## 配置的方式
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231658291.png)

## 接口的方式
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231659677.png)

## 生命周期描述
+ 初始化容器
  + 1.创建对象（内存分配）
  + 2.执行构造方法
  + 3.执行属性注入（set操作）
  + 4.执行bean初始化方法
+ 使用bean
  + 执行业务操作
+ 关闭/销毁容器
  + 执行bean销毁方法

## bean销毁时机
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302231703590.png)

# 依赖注入方式(DI)
## 分析
+ 思考：向一个类中传递数据的方式有几种？
  + 普通方法（set方法）
  + 构造方法
+ 思考：依赖注入描述了在容器中建立bean与bean之间依赖关系的过程，如果bean运行需要的是数字或字符串呢？
  + 引用类型
  + 简单类型（基本数据类型与String）
+ 依赖注入方式
  + setter注入
    + 简单类型
      + ![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232239001.png)
    + <font color="red">引用类型</font>(这是上文之前实现过的方式)
      + ![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232234232.png)
  + 构造器注入
    + 简单类型
      + 同引用类型，ref换成value即可。
    + 引用类型
      + ![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232247561.png)

## 依赖注入方式选择
![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232248827.png)

# 依赖自动装配(di_autoware)

+ IoC容器根据bean所y依赖的资源在容器中自动查找并注入到bean中的过程称为自动装配。
+ 自动装配方式
  + <font color="red">按类型（常用）</font>
  + 按名称
  + 按构造方法
  + 不启用自动装配

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232301694.png)

## 依赖自动装配特征

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232302419.png)

# 集合注入(di_collection)

包括但不限于：数组、List、Set、Map、Properties...

## 注入数组对象

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232310777.png)

## 注入List对象

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232311502.png)

## 注入Set对象

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232311240.png)

## 注入Map对象

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232312481.png)

## 注入Properties

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302232312792.png)

# 加载properties文件

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241559575.png)

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241603194.png)

# 容器

## 创建容器

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241617686.png)

## 获取bean

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241618792.png)

## 容器类层次结构

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241626589.png)

## BeanFactory

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241626361.png)

# 核心、容器阶段总结

## 容器相关

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241629753.png)

## bean相关

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241629589.png)

## 依赖注入相关

![](https://cdn.jsdelivr.net/gh/Altman29/ImgHost/BLOG_PIC/202302241631928.png)

# 注解开发



![](https://i.loli.net/2019/12/25/Fze9jchtnyJXMHN.jpg) 
![](https://i.loli.net/2019/12/25/ryLVePaqkYm4TEK.jpg) 
![](https://i.loli.net/2019/12/25/gEy5Zc1Ai6VuO4N.jpg) 
![](https://i.loli.net/2019/12/25/d6QHbytlSYO4FBG.jpg ) 
![](https://i.loli.net/2019/12/25/6nepIJ1xTgufatZ.jpg) 
![](https://i.loli.net/2019/12/25/E7Jvr4eIPwUNmzq.jpg) 
! [](https://i.loli.net/2019/12/25/mh19anwBSWIkGlH.jpg) 
![](https://i.loli.net/2019/12/25/2tu9JC8ewpBFagv.jpg)





```java

public void fun(){
    //alalla
}
```



