---
title: mybatis and plus
tags:
  - mybatis
  - mybatis-plus
categories:
  - mybatis
keywords: mybatis;
index_img: /img/mybatis/myb.png
excerpt: spring和javaer的最佳拍档~
abbrlink: 85fa2e33
date: 2019-09-03 21:06:03
---
# 什么是mybatis

> 它是一款持久层框架，用于简化JDBC开发。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141840885.png)


![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141841587.png)

# mybatis快速入门

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141841896.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141841285.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141842111.png)

# mybatis代理开发

搞一个mapper接口

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141843057.png)

把UserMapper.xml放在同样包结构目录下

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141843579.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141843679.png)


# mybatis核心配置文件

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141844577.png)

比如配置别名，就不需要填写前面的包名

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141844851.png)

# 配置文件完成增删改查

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141845271.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141845376.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141845963.png)

多条件的可以看图，可以三种。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141845216.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141846686.png)

**单条件动态查询**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141846009.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141847577.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141847212.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141847396.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141847217.png)

# 注解完成增删改查

就是把sql从xml换到注解里面书写。不过复杂的sql还是是推荐用xml来写。

## 动态SQL

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141848767.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141849250.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141849832.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141849711.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403141849859.png)

# 什么是mybatis-plus

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142159798.png)

其实工作中，大多数情况都是单表的CRUD简单查询，借助Mybatis-Plus可以通过简单配置，即可快速进行单表CRUD操作，从而节省大量时间。功能丰富，分页就是一个 插件。代码生成也很优秀。

# mp快速入门

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142200669.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142200352.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142200893.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142200088.png)

>然后把xml里面的sql全部干掉，mapper里面的方法也不需要了，有爹了（BaseMapper）！


![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142201487.png)

# 常见注解

**约定大于配置**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142202253.png)

但有时候，名字不一致不符合约定，就需要一些注解来标柱。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142202647.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142202927.png)

# 常见配置

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142203813.png)

一般不需要配置，都走默认，需要的话看官网。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142203718.png)

# 核心功能-条件构造器

前面经历的都是通过主键id进行的CRUD，但是真是业务中，很多都是通过各种条件来CRUD，所以MP提供了条件构造器。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142204175.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142204733.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142205586.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142205232.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142206514.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142206748.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142206390.png)

为什么有了QueryWrapper、UpdateWrapper还需要Lambda~Wrapper呢，是因为，目前写的代码还存在硬编码，它基于lambda语法，能避免硬编码！

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142206898.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142206839.png)

# 核心功能-自定义SQL

Mp已经提供了便捷的增删改查，并且提供了各种条件构造器，为什么还要自定义呢？

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142207991.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142207626.png)

纯xml还好，如果用mp的UpdateWrapper实现，"balance = balance - 200"相当于业务代码，在**业务代码中有sql操作是不合理的**。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142208123.png)

这种也是，select中 count还起**别名**，如果还是**硬编码**就违背了开发规范。要是不用mp的话又会很麻烦，那就没有俩全的方案吗，答案就是自定义SQL。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142210892.png)

**保证了不在业务层编写sql 遵循了开发规范，同时又享受到了MP生成sql条件的便捷的特性。**

# 核心功能-Service接口

UserMapper继承了MP提供的BaseMapper，dao层的CRUD代码不用写了，很爽。
现在还有一个Service接口，有了它，Service层的代码也不用写了！！！

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142212532.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142212376.png)

**其实就是service调用mapper里面的实现。**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142214968.png)

# Service开发业务接口

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142215893.png)

# 扩展功能-代码生成

现在使用MP需要一下这些步骤，是相对固定的

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142216921.png)

如果不是User表，而是Order表，是可以自动生成的。可以使用**MyBatisPlus插件，它是Idea插件**，很好用。

**第一步**，连接数据库

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142217696.png)

**第二步**，配置生成代码的配置

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142217225.png)

**cool**

# 扩展功能-DB静态工具

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142218597.png)

IService里面的方法都是非静态的，DB静态工具都是静态的，没有泛型。所以会多一个参数，就是Class字节码，它得知道是什么类型。需要告诉他实体类类型，其他与IService接口无区别。  但是还是有应用场景的。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142218714.png)


> 有些业务，需要在UserService里面查Address，就需要注入AddressService，有些业务需要在AddressService中注入UserService，就会产生循环依赖。就可以通过静态工具来解决，不需要注入。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142219561.png)

# 扩展功能-逻辑删除

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142220735.png)

那每个语句都要改么，不用，他会**自动修改CRUD语句**。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142221812.png)

配置之后，即便是remove删除方法，它也会自动转换成update语句，把逻辑删除自动置位已删除。

# 扩展功能-JSON处理器

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142224983.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142224473.png)

**开启自动的实体与java对象的映射。**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142224075.png)

# 扩展功能-分页插件

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142225156.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142225480.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142225835.png)

**核心代码实现如下**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403142226782.png)
