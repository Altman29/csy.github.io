---
title: Spring
categories:
- Spring
tags:
- Spring
---

# Why to learn?
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
