---
title: mysql
tags:
  - mysql
categories:
  - 微服务
keywords: mysql
index_img: /img/micro/mysql.png
excerpt: 开源的关系型数据库管理系统，广泛用于应用程序开发中的数据存储和管理。它是互联网和企业应用中最受欢迎的数据库之一，拥有庞大的社区支持和丰富的扩展功能。
abbrlink: bb4eda82
date: 2022-02-22 21:03:36
---
# 存储引擎

## MySQL体系结构

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251543172.png)

**连接层**

最上层是一些客户端和链接服务，包含本地sock 通信和大多数基于客户端/服务端工具实现的类似于
TCP/IP的通信。主要完成一些类似于连接处理、授权认证、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。


**服务层**

第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如 过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定表的查询的顺序，是否利用索引等，最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存，如果缓存空间足够大，这样在解决大量读操作的环境中能够很好的提升系统的性能。


**引擎层**

存储引擎层， 存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。数据库中的索引是在存储引擎层实现的。


**存储层**

数据存储层， 主要是将数据(如: redolog、undolog、数据、索引、二进制日志、错误日志、查询
日志、慢查询日志等)存储在文件系统之上，并完成与存储引擎的交互


## 存储引擎介绍

大家可能没有听说过存储引擎，但是一定听过引擎这个词，引擎就是发动机，是一个机器的核心组件。

比如，对于舰载机、直升机、火箭来说，他们都有各自的引擎，是他们最为核心的组件。而我们在选择引擎的时候，需要在合适的场景，选择合适的存储引擎，就像在直升机上，我们不能选择舰载机的引擎一样。而对于存储引擎，也是一样，他是mysql数据库的核心，我们也需要在合适的场景选择合适的存储引擎。接下来就来介绍一下存储引擎。存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式 。存储引擎是基于表的，而不是基于库的，所以存储引擎也可被称为表类型。我们可以在创建表的时候，来指定选择的存储引擎，如果没有指定将自动选择默认的存储引擎。


**建表时指定存储引擎**

```sql
CREATE TABLE 表名(
字段1 字段1类型 [ COMMENT 字段1注释 ] ,
......
字段n 字段n类型 [COMMENT 字段n注释 ]

) ENGINE = INNODB [ COMMENT 表注释 ] ;
```


**查询当前数据库支持的存储引擎**

```sql
show engines;
```


**查询建表语句** --- 默认存储引擎: InnoDB

```sql
show create table account;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251551170.png)

可以看到，创建表时，即使我们没有指定存储疫情，数据库也会自动选择默认的存储引擎。

**查询当前数据库支持的存储引擎**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251552130.png)

**创建表 my_myisam , 并指定MyISAM存储引擎**

```sql
create table my_myisam(
	id int,
	name varchar(10)
) engine = MyISAM ;
```

**创建表 my_memory , 指定Memory存储引擎**

```sql
create table my_memory(
	id int,
	name varchar(10)
) engine = Memory ;
```

## 存储引擎特点

了解了如何在建表时如何指定存储引擎，接下来介绍下来上面重点提到的三种存储引擎 InnoDB、MyISAM、Memory的特点。

### InnoDB

InnoDB是一种兼顾高可靠性和高性能的通用存储引擎，在MySQL 5.5 之后，InnoDB是默认的MySQL 存储引擎。

**特点**

- DML操作遵循ACID模型，支持事务；
- 行级锁，提高并发访问性能；
- 支持外键FOREIGN KEY约束，保证数据的完整性和正确性；

**文件**

xxx.ibd：xxx代表的是表名，innoDB引擎的每张表都会对应这样一个**表空间文件**，存储该表的表结
构（frm-早期的、sdi-新版的）、数据和索引。
参数：innodb_file_per_table

```sql
show variable like 'innodb_file_per_table'
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251557564.png)

如果该参数开启，代表对于InnoDB引擎的表，每一张表都对应一个ibd文件。我们直接打开MySQL的数据存放目录：C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Data ，这个目录下有很多文件夹，不同的文件夹代表不同的数据库。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251558229.png)

可以看到里面有很多的ibd文件，每一个ibd文件就对应一张表，比如：我们有一张表account，就
有这样的一个account.ibd文件，而在这个ibd文件中不仅存放表结构、数据，还会存放该表对应的
索引信息。而该文件是基于二进制存储的，不能直接基于记事本打开，我们可以使用mysql提供的一
个指令ibd2sdi ，通过该指令就可以从ibd文件中提取sdi信息，而sdi数据字典信息中就包含该表的表结构。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251558206.png)


**逻辑存储结构**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251559571.png)

- **表空间** : InnoDB存储引擎逻辑结构的最高层，ibd文件其实就是表空间文件，在表空间中可以包含多个Segment段。

- **段** : 表空间是由各个段组成的，常见的段有数据段、索引段、回滚段等。InnoDB中对于段的管理，都是引擎自身完成，不需要人为对其控制，一个段中包含多个区。

- **区** : 区是表空间的单元结构，每个区的大小为1M。默认情况下， InnoDB存储引擎页大小为16K，即一个区中一共有64个连续的页。

- **页** : 页是组成区的最小单元，页也是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB 存储引擎每次从磁盘申请 4-5 个区。

- **行** : InnoDB 存储引擎是面向行的，也就是说数据是按行进行存放的，在每一行中除了定义表时所指定的字段以外，还包含两个隐藏字段(后面会详细介绍)。

### MyISAM

MyISAM是MySQL早期的默认存储引擎。

**特点**

- 不支持事务，不支持外键
- 支持表锁，不支持行锁
- 访问速度快


**文件**

xxx.sdi：存储表结构信息

xxx.MYD: 存储数据

xxx.MYI: 存储索引

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251601566.png)
### Memory

Memory引擎的表数据时存储在内存中的，由于受到硬件问题、或断电问题的影响，只能将这些表作为临时表或缓存使用。

**特点**

- 内存存放
- hash索引（默认）

**文件**

xxx.sdi：存储表结构信息

### 区别及特点

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251603679.png)

InnoDB引擎与MyISAM引擎的区别 ?

①. InnoDB引擎, 支持事务, 而MyISAM不支持。

②. InnoDB引擎, 支持行锁和表锁, 而MyISAM仅支持表锁, 不支持行锁。

③. InnoDB引擎, 支持外键, 而MyISAM是不支持的。


## 存储引擎选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据实际情况选择多种存储引擎进行组合。

- **InnoDB**: 是Mysql的默认存储引擎，支持事务、外键。如果应用对事务的完整性有比较高的要求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操作，那么InnoDB存储引擎是比较合适的选择。

- **MyISAM** ： 如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的。

- **MEMORY**：将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。MEMORY的缺陷就是对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性。

# 索引

## 索引概述

### 介绍

索引（index）是帮助MySQL高效获取数据的数据结构(有序)。在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据， 这样就可以在这些数据结构上实现高级查找算法，这种数据结构就是索引。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251608068.png)

### 演示

表结构及其数据如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251609548.png)

假如要执行的SQL语句为 ： `select * from user where age = 45;`

**无索引情况**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251620378.png)
在无索引情况下，就需要**从第一行开始扫描，一直扫描到最后一行，我们称之为 全表扫描**，性能很低。

**有索引情况**

如果我们针对于这张表建立了索引，假设索引结构就是二叉树，那么也就意味着，会对age这个字段建立一个二叉树的索引结构。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251621873.png)
此时我们在进行查询时，只需要扫描三次就可以找到数据了，极大的提高的查询的效率。

### 特点

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251622634.png)

## 索引结构

### 概述

MySQL的索引是在存储引擎层实现的，不同的存储引擎有不同的索引结构，主要包含以下几种：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251622499.png)

上述是MySQL中所支持的所有的索引结构，接下来，我们再来看看不同的存储引擎对于索引结构的支持情况。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251623623.png)

平常所说的索引，如果没有特别指明，都是指B+树结构组织的索引。


### 二叉树

假如说MySQL的索引结构采用二叉树的数据结构，比较理想的结构如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251628492.png)
如果主键是顺序插入的，则会形成一个单向链表，结构如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251628442.png)

所以，如果选择二叉树作为索引结构，会存在以下缺点：

- 顺序插入时，会形成一个链表，查询性能大大降低。
- 大数据量情况下，层级较深，检索速度慢。

此时大家可能会想到，我们可以选择红黑树，红黑树是一颗自平衡二叉树，那这样即使是顺序插入数据，最终形成的数据结构也是一颗平衡的二叉树,结构如下:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251631197.png)
但是，即使如此，由于红黑树也是一颗二叉树，所以也会存在一个缺点：

- 大数据量情况下，层级较深，检索速度慢


所以，在MySQL的索引结构中，并没有选择二叉树或者红黑树，而选择的是B+Tree，那么什么是B+Tree呢？在详解B+Tree之前，先来介绍一个B-Tree。

### B Tree

B-Tree，B树是一种多叉路平衡查找树，相对于二叉树，B树每个节点可以有多个分支，即多叉。以一颗最大度数（max-degree）为5(5阶)的b-tree为例，那这个B树每个节点最多存储4个key，5个指针：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251632607.png)

树的度数指的是一个节点的子节点个数。

[数据结构可视化网站演示](https://www.cs.usfca.edu/~galles/visualization)

**特点**

- 5阶的B树，每一个节点最多存储4个key，对应5个指针。

- 一旦节点存储的key数量到达5，就会裂变，中间元素向上分裂。

- 在B树中，非叶子节点和叶子节点都会存放数据。

### B+ Tree

B+Tree是B-Tree的变种，我们以一颗最大度数（max-degree）为4（4阶）的b+tree为例，来看一

下其结构示意图：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251837841.png)

我们可以看到，两部分：

- 绿色框框起来的部分，是**索引部分**，仅仅起到索引数据的作用，不存储数据。

- 红色框框起来的部分，是**数据存储部分**，在其叶子节点中要存储具体的数据。

上述看到的结构是标准的B+Tree的数据结构，接下来，我们再来看看MySQL中优化之后的B+Tree。


MySQL索引数据结构对经典的B+Tree进行了优化。在原B+Tree的基础上，增加一个指向相邻叶子节点的链表指针，就形成了带有顺序指针的B+Tree，提高区间访问的性能，利于排序。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251841391.png)
### Hash

MySQL中除了支持B+Tree索引，还支持一种索引类型---Hash索引。

**结构**

哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在hash表中。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251842675.png)

如果两个(或多个)键值，映射到一个相同的槽位上，他们就产生了hash冲突（也称为hash碰撞），可以通过链表来解决。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251843199.png)

**特点**

A. Hash索引只能用于对等比较(=，in)，不支持范围查询（between，>，< ，...）。

B. 无法利用索引完成排序操作。

C. 查询效率高，通常(不存在hash冲突的情况)只需要一次检索就可以了，效率通常要高于B+tree索引。

**存储引擎支持**

在MySQL中，支持hash索引的是Memory存储引擎。 而InnoDB中具有自适应hash功能，hash索引是InnoDB存储引擎根据B+Tree索引在指定条件下自动构建的。


> 为什么InnoDB存储引擎选择使用B+tree索引结构?
> 
> A. 相对于二叉树，层级更少，搜索效率高；
> 
> B. 对于B-tree，无论是叶子节点还是非叶子节点，都会保存数据，这样导致一页中存储的键值减少，指针跟着减少，要同样保存大量数据，只能增加树的高度，导致性能降低；
> 
> C. 相对Hash索引，B+tree支持范围匹配及排序操作；

## 索引分类

### 索引分类

在MySQL数据库，将索引的具体类型主要分为以下几类：主键索引、唯一索引、常规索引、全文索引。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251845389.png)


### 聚集索引&二级索引

在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251846040.png)


聚集索引选取规则:

- 如果存在主键，主键索引就是聚集索引。
- 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引。
- 如果表没有主键，或没有合适的唯一索引，则InnoDB会自动生成一个rowid作为隐藏的聚集索引。

聚集索引和二级索引的具体结构如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251848725.png)

- 聚集索引的叶子节点下挂的是这一行的数据 。

- 二级索引的叶子节点下挂的是该字段值对应的主键值。

**示例**

接下来，我们来分析一下，当我们执行如下的SQL语句时，具体的查找过程是什么样子的。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251849718.png)

具体过程如下:

①. 由于是根据name字段进行查询，所以先根据name='Arm'到name字段的二级索引中进行匹配查
找。但是在二级索引中只能查找到 Arm 对应的主键值 10。

②. 由于查询返回的数据是*，所以此时，还需要根据主键值10，到聚集索引中查找10对应的记录，最终找到10对应的行row。

③. 最终拿到这一行的数据，直接返回即可。

>回表查询： 这种先到二级索引中查找数据，找到主键值，然后再到聚集索引中根据主键值，获取数据的方式，就称之为回表查询。


> 以下两条SQL语句，那个执行效率高? 为什么?
> A. select * from user where id = 10 ;
> B. select * from user where name = 'Arm' ;
> 备注: id为主键，name字段创建的有索引；
> 
> 解答：A 语句的执行性能要高于B 语句。
> 因为A语句直接走聚集索引，直接返回数据。 而B语句需要先查询name字段的二级索引，然后再查询聚集索引，也就是需要进行回表查询。


>InnoDB主键索引的B+tree高度为多高呢?
>
>如果树的高度为2，则可以存储 18000 多条记录。
>如果树的高度为3，则可以存储 2200w 左右的记录。

## 索引语法

**创建索引**

```sql
CREATE [ UNIQUE | FULLTEXT ] INDEX index_name ON table_name 
(index_col_name,... ) ;
```

**查看索引**

```sql
SHOW INDEX FROM table_name ;
```

**删除索引**

```sql
DROP INDEX index_name ON table_name ;
```

**示例**

A. name字段为姓名字段，该字段的值可能会重复，为该字段创建索引。

```sql
CREATE INDEX idx_user_name ON tb_user(name);
```

B. phone手机号字段的值，是非空，且唯一的，为该字段创建唯一索引。

```sql
CREATE UNIQUE INDEX idx_user_phone ON tb_user(phone);
```

C. 为profession、age、status创建联合索引。

```sql
CREATE INDEX idx_user_pro_age_sta ON tb_user(profession,age,status);
```

D. 为email建立合适的索引来提升查询效率。

```sql
CREATE INDEX idx_email ON tb_user(email);
```

## SQL性能分析

### SQL执行频率

MySQL 客户端连接成功后，通过 show [session|global] status 命令可以提供服务器状态信息。通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次：

```sql
-- session 是查看当前会话 ;

-- global 是查询全局数据 ;

SHOW GLOBAL STATUS LIKE 'Com_______';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251859574.png)

- Com_delete: 删除次数
- Com_insert: 插入次数
- Com_select: 查询次数
- Com_update: 更新次数

可以在当前数据库再执行几次查询操作，然后再次查看执行频次，看看 Com_select 参数会不会变化。

>通过上述指令，我们可以查看到当前数据库到底是以查询为主，还是以增删改为主，从而为数据库优化提供参考依据。 如果是以增删改为主，我们可以考虑不对其进行索引的优化。 如果是以查询为主，那么就要考虑对数据库的索引进行优化了。

### 慢查询日志

通过查询SQL的执行频次，我们就能够知道当前数据库到底是增删改为主，还是查询为主。 那假如说是以查询为主，我们又该如何定位针对于那些查询语句进行优化呢？ 可以借助于慢查询日志。

慢查询日志记录了所有执行时间超过指定参数（long_query_time，单位：秒，默认10秒）的所有SQL语句的日志。MySQL的慢查询日志默认没有开启，我们可以查看一下系统变量 `slow_query_log`。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251904213.png)

如果要开启慢查询日志，需要在MySQL的配置文件（/etc/my.cnf）中配置如下信息：

```properties
# 开启MySQL慢日志查询开关
slow_query_log=1
# 设置慢日志的时间为2秒，SQL语句执行时间超过2秒，就会视为慢查询，记录慢查询日志
long_query_time=2
```

配置完毕之后，通过以下指令重新启动MySQL服务器进行测试，查看慢日志文件中记录的信息/var/lib/mysql/localhost-slow.log。

```sh
systemctl restart mysqld
```

然后，再次查看开关情况，慢查询日志就已经打开了。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251906296.png)

**测试**

```sql
select * from tb_user; -- 这条SQL执行效率比较高, 执行耗时 0.00sec

select count(*) from tb_sku; -- 由于tb_sku表中, 预先存入了1000w的记录, count一次,耗时13.35sec
```

**检查慢查询日志**

在慢查询日志中，只会记录执行时间超多我们预设时间（2s）的SQL，执行较快的SQL是不会记录的。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403251908395.png)

像这样，通过慢查询日志，就可以定位出执行效率比较低的SQL，从而有针对性的进行优化。

### profile详情

show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了。通过have_profiling

参数，能够看到当前MySQL是否支持profile操作：

```sql
SELECT @@have_profiling ;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252027868.png)

可以看到，当前MySQL是支持 profile操作的，但是开关是关闭的。可以通过set语句在session/global级别开启profiling：

```sql
SET profiling = 1;
```

开关已经打开了，接下来，我们所执行的SQL语句，都会被MySQL记录，并记录执行时间消耗到哪儿去了。 我们直接执行如下的SQL语句：

```sql
select * from tb_user;

select * from tb_user where id = 1;

select * from tb_user where name = '白起';

select count(*) from tb_sku;
```

执行一系列的业务SQL的操作，然后通过如下指令查看指令的执行耗时：

```sh
-- 查看每一条SQL的耗时基本情况

show profiles;

-- 查看指定query_id的SQL语句各个阶段的耗时情况

show profile for query query_id;

-- 查看指定query_id的SQL语句CPU的使用情况

show profile cpu for query query_id;
```

查看每一条SQL的耗时情况:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252030072.png)

查看指定SQL各个阶段的耗时情况 :

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252031123.png)
### explain

EXPLAIN 或者 DESC命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行过程中表如何连接和连接的顺序。

语法:

```sql
-- 直接在select语句之前加上关键字 explain / desc

EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件 ;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252031920.png)

Explain 执行计划中各个字段的含义:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252032594.png)

## 索引使用

### 验证索引效率

在讲解索引的使用原则之前，先通过一个简单的案例，来验证一下索引，看看是否能够通过索引来提升数据查询性能。在演示的时候，我们还是使用之前准备的一张表 tb_sku , 在这张表中准备了1000w的记录。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252034142.png)


这张表中id为主键，有主键索引，而其他字段是没有建立索引的。 我们先来查询其中的一条记录，看看里面的字段情况，执行如下SQL：

```sql
select * from tb_sku where id = 1\G;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252036385.png)

>在MySQL中，`\G`不是SQL语句的一部分，而是MySQL命令行客户端的一个特殊命令，用于改变结果集的显示方式。在使用`\G`时，MySQL会将查询结果按照每行一个字段的形式展示，而不是通常的表格形式。


可以看到即使有1000w的数据,根据id进行数据查询,性能依然很快，因为主键id是有索引的。 那么接下来，我们再来根据 sn 字段进行查询，执行如下SQL：

```sql
SELECT * FROM tb_sku WHERE sn = '100000003145001';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252037639.png)

我们可以看到根据sn字段进行查询，查询返回了一条数据，结果耗时 20.78sec，就是因为sn没有索引，而造成查询效率很低。

那么我们可以针对于sn字段，建立一个索引，建立了索引之后，我们再次根据sn进行查询，再来看一下查询耗时情况。

创建索引：

```sql
create index idx_sku_sn on tb_sku(sn) ;
```

然后再次执行相同的SQL语句，再次查看SQL的耗时。

```sql
SELECT * FROM tb_sku WHERE sn = '100000003145001';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252038627.png)

sn字段建立了索引之后，查询性能大大提升。建立索引前后，查询耗时都不是一个数量级的。

### 索引失效

#### 最左前缀法则

如果索引了多列（联合索引），要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，索引将会部分失效(后面的字段索引失效)。以 tb_user 表为例，我们先来查看一下之前 tb_user 表所创建的索引。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252040077.png)

在 tb_user 表中，有一个联合索引，这个联合索引涉及到三个字段，顺序分别为：profession，age，status。

对于最左前缀法则指的是，查询时，最左变的列，也就是profession必须存在，否则索引全部失效。而且中间不能跳过某一列，否则该列后面的字段索引将失效。

>当执行SQL语句: explain select * from tb_user where age = 31 and status = '0' and profession = '软件工程'； 时，是否满足最左前缀法则，走不走上述的联合索引，索引长度？
>
>![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252043501.png)
>
>可以看到，是完全满足最左前缀法则的，索引长度54，联合索引是生效的。
>注意 ： 最左前缀法则中指的最左边的列，是指在查询时，联合索引的最左边的字段(即是第一个字段)必须**存在**，与编写SQL时，条件编写的先后顺序无关。


#### 范围查询

联合索引中，出现范围查询(>,<)，范围查询右侧的列索引失效。

```sql
explain select * from tb_user where profession = '软件工程' and age > 30 and status = '0';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252045470.png)

当范围查询使用> 或 < 时，走联合索引了，但是索引的长度为49，就说明范围查询右边的status字段是没有走索引的。

```sql
explain select * from tb_user where profession = '软件工程' and age >= 30 and status = '0';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252047065.png)

当范围查询使用>= 或 <= 时，走联合索引了，但是索引的长度为54，就说明所有的字段都是走索引的。

> 所以，在业务允许的情况下，尽可能的使用类似于 >= 或 <= 这类的范围查询，而避免使用 > 或 <。

#### 索引列运算

不要在索引列上进行运算操作， 索引将失效。

在tb_user表中，除了前面介绍的联合索引之外，还有一个索引，是phone字段的单列索引。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252048922.png)

```sql
explain select * from tb_user where substring(phone,10,2) = '15';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252051430.png)

#### 字符串不加引号

字符串类型字段使用时，不加引号，索引将失效。
通过两组示例，来看看对于字符串类型的字段，加单引号与不加单引号的区别：

```sql
explain select * from tb_user where profession = '软件工程' and age = 31 and status

= '0';

explain select * from tb_user where profession = '软件工程' and age = 31 and status

= 0;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252053628.png)

```sql
explain select * from tb_user where phone = '17799990015';

explain select * from tb_user where phone = 17799990015;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252054175.png)

经过上面两组示例，明显的发现，如果字符串不加单引号，对于查询结果，没什么影响，但是数据库存在隐式类型转换，索引将失效。

#### 模糊查询

如果仅仅是尾部模糊匹配，索引不会失效。如果是头部模糊匹配，索引失效。

接下来，我们来看一下这三条SQL语句的执行效果，查看一下其执行计划：

由于下面查询语句中，都是根据profession字段查询，符合最左前缀法则，联合索引是可以生效的，

我们主要看一下，模糊查询时，%加在关键字之前，和加在关键字之后的影响。

```sql
explain select * from tb_user where profession like '软件%';

explain select * from tb_user where profession like '%工程';

explain select * from tb_user where profession like '%工%';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252055882.png)

经过上述的测试，我们发现，在like模糊查询中，在关键字后面加%，索引可以生效。而如果在关键字前面加了%，索引将会失效。
#### or连接条件

用or分割开的条件， 如果or前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到。

```sql
explain select * from tb_user where id = 10 or age = 23;

explain select * from tb_user where phone = '17799990017' or age = 23;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252057762.png)

由于age没有索引，所以即使id、phone有索引，索引也会失效。所以需要针对于age也要建立索引。

然后，我们可以对age字段建立索引。

```sql
create index idx_user_age on tb_user(age);
```

建立了索引之后，我们再次执行上述的SQL语句，看看前后执行计划的变化。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252057038.png)

当or连接的条件，左右两侧字段都有索引时，索引才会生效。

#### 数据分布影响

如果MySQL评估使用索引比全表更慢，则不使用索引。

```sql
select * from tb_user where phone >= '17799990005';

select * from tb_user where phone >= '17799990015';
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252059219.png)

相同的SQL语句，只是传入的字段值不同，最终的执行计划也完全不一样，这是为什么呢？

就是因为MySQL在查询时，会评估使用索引的效率与走全表扫描的效率，如果走全表扫描更快，则放弃索引，走全表扫描。 因为索引是用来索引少量数据的，如果通过索引查询返回大批量的数据，则还不如走全表扫描来的快，此时索引就会失效。

再来看看 is null 与 is not null 操作是否走索引。

执行如下两条语句 ：

```sql
explain select * from tb_user where profession is null;

explain select * from tb_user where profession is not null;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252100852.png)

接下来，我们做一个操作将profession字段值全部更新为null。

然后，再次执行上述的两条SQL，查看SQL语句的执行计划。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252101570.png)

最终我们看到，一模一样的SQL语句，先后执行了两次，结果查询计划是不一样的，为什么会出现这种现象，这是和数据库的数据分布有关系。查询时MySQL会评估，走索引快，还是全表扫描快，如果全表扫描更快，则放弃索引走全表扫描。 因此，is null 、is not null是否走索引，得具体情况具体分析，并不是固定的。

### SQL提示

目前tb_user表的数据情况如下:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252103691.png)

索引情况如下:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252104353.png)

把上述的 idx_user_age, idx_email 这两个之前测试使用过的索引直接删除。

```sql
drop index idx_user_age on tb_user;

drop index idx_email on tb_user;
```


A. 执行SQL : explain select * from tb_user where profession = '软件工程';

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252106709.png)

查询走了联合索引。

B. 执行SQL，创建profession的单列索引：`create index idx_user_pro on tb_user(profession);`

C. 创建单列索引后，再次执行A中的SQL语句，查看执行计划，看看到底走哪个索引。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252107173.png)

测试结果，我们可以看到，possible_keys中 idx_user_pro_age_sta,idx_user_pro 这两个索引都可能用到，最终MySQL选择了idx_user_pro_age_sta索引。这是MySQL自动选择的结果。

那么，能不能在查询的时候，自己来指定使用哪个索引呢？ 答案是肯定的，此时就可以借助于MySQL的SQL提示来完成。 接下来，介绍一下SQL提示。

SQL提示，是优化数据库的一个重要手段，简单来说，就是在SQL语句中加入一些人为的提示来达到优化操作的目的。

1). `use index` ： 建议MySQL使用哪一个索引完成此次查询（仅仅是建议，mysql内部还会再次进行评估）。

```sql
explain select * from tb_user use index(idx_user_pro) where profession = '软件工程';
```

2). `ignore index` ： 忽略指定的索引。

```sql
explain select * from tb_user ignore index(idx_user_pro) where profession = '软件工程';
```

3). `force index` ： 强制使用索引。

```sql
explain select * from tb_user force index(idx_user_pro) where profession = '软件工程';
```

### 覆盖索引(减少回表)

尽量使用**覆盖索引**，减少`select * `。 那么什么是覆盖索引呢？ 覆盖索引是指 **查询使用了索引，并且需要返回的列，在该索引中已经全部能够找到 。**

接下来，来看一组SQL的执行计划，看看执行计划的差别，然后再来具体做一个解析。

```sql
explain select id, profession from tb_user where profession = '软件工程' and age = 31 and status = '0' ;

explain select id,profession,age, status from tb_user where profession = '软件工程' and age = 31 and status = '0' ;

explain select id,profession,age, status, name from tb_user where profession = '软件工程' and age = 31 and status = '0' ;

explain select * from tb_user where profession = '软件工程' and age = 31 and status = '0';
```

上述这几条SQL的执行结果为:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252114859.png)

从上述的执行计划可以看到，这四条SQL语句的执行计划前面所有的指标都是一样的，看不出来差异。但是此时，我们主要关注的是后面的Extra，前面两天SQL的结果为 `Using where; Using Index ;` 而后面两条SQL的结果为: `Using index condition `。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252115109.png)

因为，在tb_user表中有一个联合索引 idx_user_pro_age_sta，该索引关联了三个字段profession、age、status，而这个索引也是一个二级索引，所以叶子节点下面挂的是这一行的主键id。 所以当我们查询返回的数据在 id、profession、age、status 之中，则直接走二级索引直接返回数据了。 如果超出这个范围，就需要拿到主键id，再去扫描聚集索引，再获取额外的数据了，这个过程就是回表。 而我们如果一直使用select \* 查询返回所有字段值，很容易就会造成回表查询（除非是根据主键查询，此时只会扫描聚集索引）。

### 前缀索引

当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让索引变得很大，查询时，浪费大量的磁盘IO， 影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

**1). 语法**

```sql
create index idx_xxxx on table_name(column(n)) ;
```

示例:

为tb_user表的email字段，建立长度为5的前缀索引。

```sql
create index idx_email_5 on tb_user(email(5));
```

**2). 前缀长度**

可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值，索引选择性越高则查询效率越高， 唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。

```sql
select count(distinct email) / count(*) from tb_user ;

select count(distinct substring(email,1,5)) / count(*) from tb_user ;
```

**3). 前缀索引的查询流程**

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252124503.png)

### 单列索引与联合索引

单列索引：即一个索引只包含单个列。

联合索引：即一个索引包含了多个列。

先来看看 tb_user 表中目前的索引情况:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252125912.png)

在查询出来的索引中，既有单列索引，又有联合索引。

接下来，我们来执行一条SQL语句，看看其执行计划：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252125570.png)

通过上述执行计划我们可以看出来，在and连接的两个字段 phone、name上都是有单列索引的，但是最终mysql只会选择一个索引，也就是说，只能走一个字段的索引，此时是会回表查询的。

紧接着，我们再来创建一个phone和name字段的联合索引来查询一下执行计划。

```sql
create unique index idx_user_phone_name on tb_user(phone,name);
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252126404.png)

此时，查询时，就走了联合索引，而在联合索引中包含 phone、name的信息，在叶子节点下挂的是对应的主键id，所以查询是无需回表查询的。

>在业务场景中，如果存在多个查询条件，考虑针对于查询字段建立索引时，建议建立联合索引，而非单列索引。

如果查询使用的是联合索引，具体的结构示意图如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252128828.png)

## 索引设计原则

1). 针对于数据量较大，且查询比较频繁的表建立索引。

2). 针对于常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引。

3). 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高。

4). 如果是字符串类型的字段，字段的长度较长，可以针对于字段的特点，建立前缀索引。

5). 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率。

6). 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率。

7). 如果索引列不能存储NULL值，请在创建表时使用NOT NULL约束它。当优化器知道每列是否包含NULL值时，它可以更好地确定哪个索引最有效地用于查询。

# SQL优化

## 插入数据

### insert

如果我们需要一次性往数据库表中插入多条记录，可以从以下三个方面进行优化。

```sql
insert into tb_test values(1,'tom');

insert into tb_test values(2,'cat');

insert into tb_test values(3,'jerry');
```

1). 优化方案一： **批量插入数据**

```sql
insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');
```

2). 优化方案二：**手动控制事务**

```sql
start transaction;

insert into tb_test values(1,'Tom'),(2,'Cat'),(3,'Jerry');

insert into tb_test values(4,'Tom'),(5,'Cat'),(6,'Jerry');

insert into tb_test values(7,'Tom'),(8,'Cat'),(9,'Jerry');

commit;
```

3). 优化方案三：**主键顺序插入，性能要高于乱序插入。**

```sql
主键乱序插入 : 8 1 9 21 88 2 4 15 89 5 7 3

主键顺序插入 : 1 2 3 4 5 7 8 9 15 21 88 89
```

### 大批量插入数据

如果一次性需要插入大批量数据(比如: 几百万的记录)，使用insert语句插入性能较低，此时可以使用MySQL数据库提供的load指令进行插入。操作如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252133835.png)

可以执行如下指令，将数据脚本文件中的数据加载到表结构中：

```sql
-- 客户端连接服务端时，加上参数 -–local-infile
mysql –-local-infile -u root -p

-- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;

-- 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table tb_user fields terminated by ',' lines terminated by '\n' ;
```

插入100w的记录，17s就完成了，性能很好。

## 主键优化

主键顺序插入的性能是要高于乱序插入的。 这里介绍一下具体的原因。

**1). 数据组织方式**

在InnoDB存储引擎中，表数据都是根据主键顺序组织存放的，这种存储方式的表称为**索引组织表**(index organized table IOT)。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252136498.png)

行数据，都是存储在**聚集索引的叶子节点上**的。而之前也了解过InnoDB的逻辑结构图：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252136639.png)

在InnoDB引擎中，数据行是记录在逻辑结构 page 页中的，而每一个页的大小是固定的，默认16K。那也就意味着， 一个页中所存储的行也是有限的，如果插入的数据行row在该页存储不小，将会存储到下一个页中，页与页之间会通过指针连接。

**2). 页分裂**

页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据过大，会行溢出)，根据主键排列。

A. 主键顺序插入效果

①. 从磁盘中申请页， 主键顺序插

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252138407.png)

②. 第一个页没有满，继续往第一页插

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252138509.png)

③. 当第一个也写满之后，再写入第二个页，页与页之间会通过指针连接

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252139884.png)

④. 当第二页写满了，再往第三页写入

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252139947.png)

B. 主键乱序插入效果

①. 加入1#,2#页都已经写满了，存放了如图所示的数据

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252139582.png)

②. 此时再插入id为50的记录，我们来看看会发生什么现象

会再次开启一个页，写入新的页中吗？
![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252140064.png)


不会。因为，索引结构的叶子节点是有顺序的。按照顺序，应该存储在47之后。
![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252140880.png)

但是47所在的1#页，已经写满了，存储不了50对应的数据了。 那么此时会开辟一个新的页 3#。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252140484.png)

但是并不会直接将50存入3#页，而是会将1#页后一半的数据，移动到3#页，然后在3#页，插入50。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252141983.png)

移动数据，并插入id为50的数据之后，那么此时，这三个页之间的数据顺序是有问题的。 1#的下一个页，应该是3#， 3#的下一个页是2#。 所以，此时，需要重新设置链表指针。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252142866.png)
上述的这种现象，称之为 "**页分裂**"，是比较耗费性能的操作。

**3). 页合并**

目前表中已有数据的索引结构(叶子节点)如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252142724.png)

当我们对已有数据进行删除时，具体的效果如下:

当删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252143485.png)

当我们继续删除2#的数据记录

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252143947.png)
当页中删除的记录达到 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前或后）看看是否可以将两个页合并以优化空间使用。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252144755.png)

删除数据，并将页合并之后，再次插入新的数据21，则直接插入3#页

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252144827.png)

这个里面所发生的合并页的这个现象，就称之为 "**页合并**"。

## order by优化

MySQL的排序，有两种方式：

**Using filesort** : 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 FileSort 排序。

**Using index** : 通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要额外排序，操作效率高。

对于以上的两种排序方式，Using index的性能高，而Using filesort的性能低，我们在优化排序操作时，尽量要优化为 Using index。所以，为排序条件设置索引是很有必要的。

## group by优化

分组操作，我们主要来看看索引对于分组操作的影响。

首先我们先将 tb_user 表的索引全部删除掉 。

```sql
drop index idx_user_pro_age_sta on tb_user;

drop index idx_email_5 on tb_user;

drop index idx_user_age_phone_aa on tb_user;

drop index idx_user_age_phone_ad on tb_user;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252204428.png)

接下来，在没有索引的情况下，执行如下SQL，查询执行计划：

```sql
explain select profession , count(*) from tb_user group by profession ;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252205571.png)

然后再针对于 profession ， age， status 创建一个联合索引。

```sql
create index idx_user_pro_age_sta on tb_user(profession , age , status);
```

紧接着，再执行前面相同的SQL查看执行计划。

```sql
explain select profession , count(*) from tb_user group by profession ;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252206618.png)

再执行如下的分组查询SQL，查看执行计划：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252207087.png)

发现，如果仅仅根据age分组，就会出现` Using temporary` ；而如果是 根据profession,age两个字段同时分组，则不会出现` Using temporary`。原因是因为**对于分组操作，在联合索引中，也是符合最左前缀法则的**。

所以，在分组操作中，我们需要通过以下两点进行优化，以提升性能：

- A. 在分组操作时，可以通过索引来提高效率。
- B. 分组操作时，索引的使用也是满足最左前缀法则的。

## limit优化

在数据量比较大时，如果进行limit分页查询，在查询时，越往后，分页查询效率越低。

我们一起来看看执行limit分页查询耗时对比：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252209470.png)

越往后，分页查询效率越低，这就是分页查询的问题所在。

因为，当在进行分页查询时，如果执行 limit 2000000,10 ，此时需要MySQL排序前2000010 记录，仅仅返回 2000000 - 2000010 的记录，其他记录丢弃，查询排序的代价非常大 。

优化思路: 一般分页查询时，通过创建 覆盖索引 能够比较好地提高性能，可以通过**覆盖索引加子查询**形式进行优化。

```sql
explain select * from tb_sku t , (select id from tb_sku order by id limit 2000000,10) a where t.id = a.id;
```
## count优化

```sql
select count(*) from tb_user ;
```

在之前的测试中，如果数据量很大，在执行count操作时，是非常耗时的。

- MyISAM 引擎把一个表的总行数存在了磁盘上，因此执行 count(\*) 的时候会直接返回这个数，效率很高； 但是如果是带条件的count，MyISAM也慢。
- InnoDB 引擎就麻烦了，它执行 count(\*) 的时候，需要把数据一行一行地从引擎里面读出来，然后累积计数。

如果说要大幅度提升InnoDB表的count效率，主要的优化思路：自己计数(可以借助于redis这样的数
据库进行,但是如果是带条件的count又比较麻烦了)。

count() 是一个聚合函数，对于返回的结果集，一行行地判断，如果 count 函数的参数不是NULL，累计值就加 1，否则不加，最后返回累计值。用法：count（\*）、count（主键）、count（字段）、count（数字）。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403252213776.png)

>按照效率排序的话，count(字段) < count(主键 id) < count(1) ≈ count(\*)，所以尽量使用 count(\*)。


## update优化

需要注意一下update语句执行时的注意事项。

```sql
update course set name = 'javaEE' where id = 1 ;
```

当在执行SQL语句时，会锁定id为1这一行的数据，然后事务提交之后，**行锁**释放。

但是当执行如下SQL时。

```sql
update course set name = 'SpringBoot' where name = 'PHP' ;
```

在执行上述的SQL时，发现**行锁**升级为了**表锁**。 导致该update语句的性能大大降低。

>InnoDB的行锁是针对索引加的锁，不是针对记录加的锁 ,并且该索引不能失效，否则会从行锁升级为表锁 。

# 视图&存储过程&触发器

## 视图

### 概述

视图（View）是一种虚拟存在的表。视图中的数据并不在数据库中实际存在，行和列数据来自定义视图的查询中使用的表，并且是在使用视图时动态生成的。

通俗的讲，视图只保存了查询的SQL逻辑，不保存查询结果。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。

### 语法

**1). 创建**

```sql
CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED | LOCAL ] CHECK OPTION ]
```

**2). 查询**

```sql
查看创建视图语句：SHOW CREATE VIEW 视图名称;

查看视图数据：SELECT * FROM 视图名称 ...... ;
```


**3). 修改**

```sql
方式一：CREATE [OR REPLACE] VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH

[ CASCADED | LOCAL ] CHECK OPTION ]

方式二：ALTER VIEW 视图名称[(列名列表)] AS SELECT语句 [ WITH [ CASCADED |

LOCAL ] CHECK OPTION ]
```

**4). 删除**

```sql
DROP VIEW [IF EXISTS] 视图名称 [,视图名称] ...
```

**示例**

```sql
-- 创建视图

create or replace view stu_v_1 as select id,name from student where id <= 10;

-- 查询视图

show create view stu_v_1;

select * from stu_v_1;

select * from stu_v_1 where id < 3;

-- 修改视图

create or replace view stu_v_1 as select id,name,no from student where id <= 10;

alter view stu_v_1 as select id,name from student where id <= 10;

-- 删除视图

drop view if exists stu_v_1;
```

接下来，做一个测试。

```sql
insert into stu_v_1 values(6,'Tom');

insert into stu_v_1 values(17,'Tom22');
```

因为我们在创建视图的时候，指定的条件为 id<=10, id为17的数据，是不符合条件的，所以没有查询出来，但是这条数据确实是已经成功的插入到了基表中。

### 检查选项

当使用WITH CHECK OPTION子句创建视图时，MySQL会通过视图检查正在更改的每个行，例如 插入，更新，删除，以使其符合视图的定义。 MySQL允许基于另一个视图创建视图，它还会检查依赖视图中的规则以保持一致性。为了确定检查的范围，mysql提供了两个选项： CASCADED 和 LOCAL，默认值为 CASCADED 。

**1). CASCADED**

级联。

比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 cascaded，但是v1视图创建时未指定检查选项。 则在执行检查时，不仅会检查v2，还会级联检查v2的关联视图v1。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261001639.png)

**2). LOCAL**

本地。

比如，v2视图是基于v1视图的，如果在v2视图创建的时候指定了检查选项为 local ，但是v1视图创建时未指定检查选项。 则在执行检查时，知会检查v2，不会检查v2的关联视图v1。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261001673.png)

### 视图的更新

要使视图可更新，视图中的行与基础表中的行之间必须存在一对一的关系。如果视图包含以下任何一项，则该视图不可更新：

A. 聚合函数或窗口函数（SUM()、 MIN()、 MAX()、 COUNT()等）

B. DISTINCT

C. GROUP BY

D. HAVING

E. UNION 或者 UNION ALL


**示例**

```sql
create view stu_v_count as select count(*) from student;
```

上述的视图中，就只有一个单行单列的数据，如果我们对这个视图进行更新或插入的，将会报错。

```sql
insert into stu_v_count values(10);
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261003422.png)

### 视图作用

**1). 简单**

视图不仅可以简化用户对数据的理解，也可以简化他们的操作。那些被经常使用的查询可以被定义为视图，从而使得用户不必为以后的操作每次指定全部的条件。

**2). 安全**

数据库可以授权，但不能授权到数据库特定行和特定的列上。通过视图用户只能查询和修改他们所能见到的数据。

**3). 数据独立**

视图可帮助用户屏蔽真实表结构变化带来的影响。

### 案例

1). 为了保证数据库表的安全性，开发人员在操作tb_user表时，只能看到的用户的基本字段，屏蔽手机号和邮箱两个字段。

```sql
create view tb_user_view as select id,name,profession,age,gender,status,createtime from tb_user;

select * from tb_user_view;
```

2). 查询每个学生所选修的课程（三张表联查），这个功能在很多的业务中都有使用到，为了简化操作，定义一个视图。

```sql
create view tb_stu_course_view as select s.name student_name , s.no student_no , c.name course_name from student s, student_course sc , course c where s.id = sc.studentid and sc.courseid = c.id;

select * from tb_stu_course_view;
```
## 存储过程

### 介绍

存储过程是事先经过编译并存储在数据库中的一段 SQL 语句的集合，调用存储过程可以简化应用开发人员的很多工作，减少数据在数据库和应用服务器之间的传输，对于提高数据处理的效率是有好处的。存储过程思想上很简单，就是数据库 SQL 语言层面的代码封装与重用。

**特点**

- 封装，复用 -----------------------> 可以把某一业务SQL封装在存储过程中，需要用到的时候直接调用即可。

- 可以接收参数，也可以返回数据 --------> 再存储过程中，可以传递参数，也可以接收返回值。

- 减少网络交互，效率提升 -------------> 如果涉及到多条SQL，每执行一次都是一次网络传输。 而如果封装在存储过程中，我们只需要网络交互一次可能就可以了。

### 基本语法

**1). 创建**

```sql
CREATE PROCEDURE 存储过程名称 ([ 参数列表 ])

BEGIN

-- SQL语句

END ;
```

**2). 调用**

```sql
CALL 名称 ([ 参数 ]);
```

**3). 查看**

```sql
SELECT * FROM INFORMATION_SCHEMA.ROUTINES WHERE ROUTINE_SCHEMA = 'xxx'; -- 查询指定数据库的存储过程及状态信息

SHOW CREATE PROCEDURE 存储过程名称 ; -- 查询某个存储过程的定义
```

**4). 删除**

```sql
DROP PROCEDURE [ IF EXISTS ] 存储过程名称 ；
```

**示例**

```sql
-- 存储过程基本语法

-- 创建

create procedure p1()

begin

select count(*) from student;

end;

-- 调用

call p1();

-- 查看

select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'itcast';

show create procedure p1;

-- 删除

drop procedure if exists p1;
```
### 变量

在MySQL中变量分为三种类型: 系统变量、用户定义变量、局部变量。

#### 系统变量

系统变量 是MySQL服务器提供，不是用户定义的，属于服务器层面。分为全局变量（GLOBAL）、会话变量（SESSION）。

**1). 查看系统变量**

```sql
SHOW [ SESSION | GLOBAL ] VARIABLES ; -- 查看所有系统变量

SHOW [ SESSION | GLOBAL ] VARIABLES LIKE '......'; -- 可以通过LIKE模糊匹配方式查找变量

SELECT @@[SESSION | GLOBAL] 系统变量名; -- 查看指定变量的值
```

**2). 设置系统变量**

```sql
SET [ SESSION | GLOBAL ] 系统变量名 = 值 ;

SET @@[SESSION | GLOBAL]系统变量名 = 值 ;
```

**示例**

```sql
-- 查看系统变量

show session variables ;

show session variables like 'auto%';

show global variables like 'auto%';

select @@global.autocommit;

select @@session.autocommit;

-- 设置系统变量

set session autocommit = 1;

insert into course(id, name) VALUES (6, 'ES');

set global autocommit = 0;

select @@global.autocommit;
```

#### 用户定义变量

用户定义变量 是用户根据需要自己定义的变量，用户变量不用提前声明，在用的时候直接用 "@变量名" 使用就可以。其作用域为当前连接。

**1). 赋值**

方式一:

```sql
//赋值时，可以使用 = ，也可以使用 := 。
SET @var_name = expr [, @var_name = expr] ... ;

SET @var_name := expr [, @var_name := expr] ... ;
```

方式二:

```sql
SELECT @var_name := expr [, @var_name := expr] ... ;

SELECT 字段名 INTO @var_name FROM 表名;
```

**2). 使用**

```sql
SELECT @var_name ;
```

>注意: 用户定义的变量无需对其进行声明或初始化，只不过获取到的值为NULL。

**示例**

```sql
-- 赋值

set @myname = 'itcast';

set @myage := 10;

set @mygender := '男',@myhobby := 'java';

select @mycolor := 'red';

select count(*) into @mycount from tb_user;

-- 使用

select @myname,@myage,@mygender,@myhobby;

select @mycolor , @mycount;

select @abc;
```

#### 局部变量

局部变量 是根据需要定义的在局部生效的变量，访问之前，需要DECLARE声明。可用作存储过程内的局部变量和输入参数，局部变量的范围是在其内声明的BEGIN ... END块。

**1). 声明**

```sql
DECLARE 变量名 变量类型 [DEFAULT ... ] ;
```

变量类型就是数据库字段类型：INT、BIGINT、CHAR、VARCHAR、DATE、TIME等。

**2). 赋值**

```sql
SET 变量名 = 值 ;

SET 变量名 := 值 ;

SELECT 字段名 INTO 变量名 FROM 表名 ... ;
```

**示例**

```sql
-- 声明局部变量 - declare

-- 赋值

create procedure p2()

begin

declare stu_count int default 0;

select count(*) into stu_count from student;

select stu_count;

end;

call p2();
```
### if

if 用于做条件判断，具体的语法结构为：

```sql
IF 条件1 THEN

.....

ELSEIF 条件2 THEN -- 可选

.....

ELSE -- 可选

.....

END IF;
```

在if条件判断的结构中，ELSE IF 结构可以有多个，也可以没有。 ELSE结构可以有，也可以没有。

**示例**

根据定义的分数score变量，判定当前分数对应的分数等级。

- score >= 85分，等级为优秀。

- score >= 60分 且 score < 85分，等级为及格。

- score < 60分，等级为不及格。

```sql
create procedure p3()

begin

declare score int default 58;

declare result varchar(10);

if score >= 85 then

set result := '优秀';

elseif score >= 60 then

set result := '及格';

else

set result := '不及格';

end if;

select result;

end;

call p3();
```

需求我们虽然已经实现了，但是也存在一些问题，比如：score 分数我们是在存储过程中定义死的，而且最终计算出来的分数等级，我们也仅仅是最终查询展示出来而已。能不能，把score分数动态的传递进来，计算出来的分数等级是否可以作为返回值返回呢？答案是肯定的

### 参数

1). 介绍

参数的类型，主要分为以下三种：IN、OUT、INOUT。 具体的含义如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261042345.png)

**用法**：

```sql
CREATE PROCEDURE 存储过程名称 ([ IN/OUT/INOUT 参数名 参数类型 ])

BEGIN

-- SQL语句

END ;
```

**示例**

根据传入参数score，判定当前分数对应的分数等级，并返回。

- score >= 85分，等级为优秀。

- score >= 60分 且 score < 85分，等级为及格。

- score < 60分，等级为不及格。

```sql
create procedure p4(in score int, out result varchar(10))

begin

if score >= 85 then

set result := '优秀';

elseif score >= 60 then

set result := '及格';

else

set result := '不及格';

end if;

end;

-- 定义用户变量 @result来接收返回的数据, 用户变量可以不用声明

call p4(18, @result);

select @result;
```

**示例二**

将传入的200分制的分数，进行换算，换算成百分制，然后返回。

```sql
create procedure p5(inout score double)

begin

set score := score * 0.5;

end;

set @score = 198;

call p5(@score);

select @score;
```
### case

1). 介绍

case结构及作用，和我们在基础篇中所讲解的流程控制函数很类似。有两种语法格式：

语法1：

```sql
-- 含义： 当case_value的值为 when_value1时，执行statement_list1，当值为 when_value2时，

执行statement_list2， 否则就执行 statement_list

CASE case_value

WHEN when_value1 THEN statement_list1

[ WHEN when_value2 THEN statement_list2] ...

[ ELSE statement_list ]

END CASE;
```

语法2：

```sql
-- 含义： 当条件search_condition1成立时，执行statement_list1，当条件search_condition2成

立时，执行statement_list2， 否则就执行 statement_list

CASE

WHEN search_condition1 THEN statement_list1

[WHEN search_condition2 THEN statement_list2] ...

[ELSE statement_list]

END CASE;
```

2). 案例

根据传入的月份，判定月份所属的季节（要求采用case结构）。

- 1-3月份，为第一季度

- 4-6月份，为第二季度

- 7-9月份，为第三季度

- 10-12月份，为第四季度

```sql
create procedure p6(in month int)

begin

declare result varchar(10);

case

when month >= 1 and month <= 3 then

set result := '第一季度';

when month >= 4 and month <= 6 then

set result := '第二季度';

when month >= 7 and month <= 9 then

set result := '第三季度';

when month >= 10 and month <= 12 then

set result := '第四季度';

else

set result := '非法参数';

end case ;

select concat('您输入的月份为: ',month, ', 所属的季度为: ',result);

end;

call p6(16);
```

>注意：如果判定条件有多个，多个条件之间，可以使用 and 或 or 进行连接。
### while

1). 介绍

while 循环是有条件的循环控制语句。满足条件后，再执行循环体中的SQL语句。具体语法为：

```sql
-- 先判定条件，如果条件为true，则执行逻辑，否则，不执行逻辑

WHILE 条件 DO

SQL逻辑...

END WHILE;
```

2). 案例

计算从1累加到n的值，n为传入的参数值。

```sql
-- A. 定义局部变量, 记录累加之后的值;

-- B. 每循环一次, 就会对n进行减1 , 如果n减到0, 则退出循环

create procedure p7(in n int)

begin

declare total int default 0;

while n>0 do

set total := total + n;

set n := n - 1;

end while;

select total;

end;

call p7(100);
```
### repeat

1). 介绍

repeat是有条件的循环控制语句, 当满足until声明的条件的时候，则退出循环 。具体语法为：

```sql
-- 先执行一次逻辑，然后判定UNTIL条件是否满足，如果满足，则退出。如果不满足，则继续下一次循环

REPEAT

SQL逻辑...

UNTIL 条件

END REPEAT;
```

2). 案例

计算从1累加到n的值，n为传入的参数值。(使用repeat实现)

```sql
-- A. 定义局部变量, 记录累加之后的值;

-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环

create procedure p8(in n int)

begin

declare total int default 0;

repeat

set total := total + n;

set n := n - 1;

until n <= 0

end repeat;

select total;

end;

call p8(10);

call p8(100);
```
### loop

1). 介绍

LOOP 实现简单的循环，如果不在SQL逻辑中增加退出循环的条件，可以用其来实现简单的死循环。

LOOP可以配合一下两个语句使用：

LEAVE ：配合循环使用，退出循环。

ITERATE：必须用在循环中，作用是跳过当前循环剩下的语句，直接进入下一次循环。

```sql
[begin_label:] LOOP

SQL逻辑...

END LOOP [end_label];
```

```sql
LEAVE label; -- 退出指定标记的循环体

ITERATE label; -- 直接进入下一次循环
```

上述语法中出现的 begin_label，end_label，label 指的都是我们所自定义的标记。

2). 案例一

计算从1累加到n的值，n为传入的参数值。

```sql
-- A. 定义局部变量, 记录累加之后的值;

-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx

create procedure p9(in n int)

begin

declare total int default 0;

sum:loop

if n<=0 then

leave sum;

end if;

set total := total + n;

set n := n - 1;

end loop sum;

select total;

end;

call p9(100);
```

3). 案例二

计算从1到n之间的偶数累加的值，n为传入的参数值。

```sql
-- A. 定义局部变量, 记录累加之后的值;

-- B. 每循环一次, 就会对n进行-1 , 如果n减到0, 则退出循环 ----> leave xx

-- C. 如果当次累加的数据是奇数, 则直接进入下一次循环. --------> iterate xx

create procedure p10(in n int)

begin

declare total int default 0;

sum:loop

if n<=0 then

leave sum;

end if;

if n%2 = 1 then

set n := n - 1;

iterate sum;

end if;

set total := total + n;

set n := n - 1;

end loop sum;

select total;

end;

call p10(100);
```
### 游标

1). 介绍

游标（CURSOR）是用来存储查询结果集的数据类型 , 在存储过程和函数中可以使用游标对结果集进

行循环的处理。游标的使用包括游标的声明、OPEN、FETCH 和 CLOSE，其语法分别如下。

A. 声明游标

```sql
DECLARE 游标名称 CURSOR FOR 查询语句 ;
```

B. 打开游标

```sql
OPEN 游标名称 ;
```

C. 获取游标记录

```sql
FETCH 游标名称 INTO 变量 [, 变量 ] ;
```

D. 关闭游标

```sql
CLOSE 游标名称 ;
```

2). 案例

根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表(id,name,profession)中。

```sql
-- 逻辑:

-- A. 声明游标, 存储查询结果集

-- B. 准备: 创建表结构

-- C. 开启游标

-- D. 获取游标中的记录

-- E. 插入数据到新表中

-- F. 关闭游标

create procedure p11(in uage int)

begin

declare uname varchar(100);

declare upro varchar(100);

declare u_cursor cursor for select name,profession from tb_user where age <=

uage;

drop table if exists tb_user_pro;

create table if not exists tb_user_pro(

id int primary key auto_increment,

name varchar(100),

profession varchar(100)

);

open u_cursor;

while true do

fetch u_cursor into uname,upro;

insert into tb_user_pro values (null, uname, upro);

end while;

close u_cursor;

end;

call p11(30);

```

上述的存储过程，最终我们在调用的过程中，会报错，之所以报错是因为上面的while循环中，并没有退出条件。当游标的数据集获取完毕之后，再次获取数据，就会报错，从而终止了程序的执行。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261053651.png)
但是此时，tb_user_pro表结构及其数据都已经插入成功了，我们可以直接刷新表结构，检查表结构中的数据。

>要想解决这个问题，就需要通过MySQL中提供的 条件处理程序 Handler 来解决。

### 条件处理

1). 介绍

条件处理程序（Handler）可以用来定义在流程控制结构执行过程中遇到问题时相应的处理步骤。具体

语法为：

```sql
DECLARE handler_action HANDLER FOR condition_value [, condition_value]

... statement ;

handler_action 的取值：

CONTINUE: 继续执行当前程序

EXIT: 终止执行当前程序

condition_value 的取值：

SQLSTATE sqlstate_value: 状态码，如 02000

SQLWARNING: 所有以01开头的SQLSTATE代码的简写

NOT FOUND: 所有以02开头的SQLSTATE代码的简写

SQLEXCEPTION: 所有没有被SQLWARNING 或 NOT FOUND捕获的SQLSTATE代码的简写
```


2). 案例

我们继续来完成在上一小节提出的这个需求，并解决其中的问题。

根据传入的参数uage，来查询用户表tb_user中，所有的用户年龄小于等于uage的用户姓名

（name）和专业（profession），并将用户的姓名和专业插入到所创建的一张新表

(id,name,profession)中。

A. 通过SQLSTATE指定具体的状态码

```sql
-- 逻辑:

-- A. 声明游标, 存储查询结果集

-- B. 准备: 创建表结构

-- C. 开启游标

-- D. 获取游标中的记录

-- E. 插入数据到新表中

-- F. 关闭游标

create procedure p11(in uage int)

begin

declare uname varchar(100);

declare upro varchar(100);

declare u_cursor cursor for select name,profession from tb_user where age <=

uage;

-- 声明条件处理程序 ： 当SQL语句执行抛出的状态码为02000时，将关闭游标u_cursor，并退出

declare exit handler for SQLSTATE '02000' close u_cursor;

drop table if exists tb_user_pro;

create table if not exists tb_user_pro(

id int primary key auto_increment,

name varchar(100),

profession varchar(100)

);

open u_cursor;

while true do

fetch u_cursor into uname,upro;

insert into tb_user_pro values (null, uname, upro);

end while;

close u_cursor;

end;

call p11(30);
```

B. 通过SQLSTATE的代码简写方式 NOT FOUND

02 开头的状态码，代码简写为 NOT FOUND

```sql
create procedure p12(in uage int)

begin

declare uname varchar(100);

declare upro varchar(100);

declare u_cursor cursor for select name,profession from tb_user where age <=

uage;

-- 声明条件处理程序 ： 当SQL语句执行抛出的状态码为02开头时，将关闭游标u_cursor，并退出

declare exit handler for not found close u_cursor;

drop table if exists tb_user_pro;

create table if not exists tb_user_pro(

id int primary key auto_increment,

name varchar(100),

profession varchar(100)

);

open u_cursor;

while true do

fetch u_cursor into uname,upro;

insert into tb_user_pro values (null, uname, upro);

end while;

close u_cursor;

end;

call p12(30);
```

## 触发器

### 介绍

触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触发并执行触发器中定义的SQL语句集合。触发器的这种特性可以协助应用在数据库端确保数据的完整性, 日志记录 , 数据校验等操作 。

使用别名OLD和NEW来引用触发器中发生变化的记录内容，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261057909.png)

### 语法

1). 创建

```sql
CREATE TRIGGER trigger_name

BEFORE/AFTER INSERT/UPDATE/DELETE

ON tbl_name FOR EACH ROW -- 行级触发器

BEGIN

trigger_stmt ;

END;
```

2). 查看

```sql
SHOW TRIGGERS ;
```

3). 删除

```sql
DROP TRIGGER [schema_name.]trigger_name ; -- 如果没有指定 schema_name，默认为当前数据库 。
```
### 示例

通过触发器记录 tb_user 表的数据变更日志，将变更日志插入到日志表user_logs中, 包含增加,修改 , 删除 ;

表结构准备:

```sql
-- 准备工作 : 日志表 user_logs

create table user_logs(

id int(11) not null auto_increment,

operation varchar(20) not null comment '操作类型, insert/update/delete',

operate_time datetime not null comment '操作时间',

operate_id int(11) not null comment '操作的ID',

operate_params varchar(500) comment '操作参数',

primary key(`id`)

)engine=innodb default charset=utf8;
```

A. 插入数据触发器`

```sql
create trigger tb_user_insert_trigger

after insert on tb_user for each row

begin

insert into user_logs(id, operation, operate_time, operate_id, operate_params)

VALUES

(null, 'insert', now(), new.id, concat('插入的数据内容为:

id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ',

profession=', NEW.profession));

end;
```

测试:

```sql
-- 查看

show triggers ;

-- 插入数据到tb_user

insert into tb_user(id, name, phone, email, profession, age, gender, status,

createtime) VALUES (26,'三皇子','18809091212','erhuangzi@163.com','软件工

程',23,'1','1',now());
```

测试完毕之后，检查日志表中的数据是否可以正常插入，以及插入数据的正确性。

B. 修改数据触发器

```sql
create trigger tb_user_update_trigger

after update on tb_user for each row

begin

insert into user_logs(id, operation, operate_time, operate_id, operate_params)

VALUES

(null, 'update', now(), new.id,

concat('更新之前的数据: id=',old.id,',name=',old.name, ', phone=',

old.phone, ', email=', old.email, ', profession=', old.profession,

' | 更新之后的数据: id=',new.id,',name=',new.name, ', phone=',

NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));

end;
```


C. 删除数据触发器

```sql
create trigger tb_user_delete_trigger

after delete on tb_user for each row

begin

insert into user_logs(id, operation, operate_time, operate_id, operate_params)

VALUES

(null, 'delete', now(), old.id,

concat('删除之前的数据: id=',old.id,',name=',old.name, ', phone=',

old.phone, ', email=', old.email, ', profession=', old.profession));

end;
```
# 锁

## 概述

锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

MySQL中的锁，按照锁的粒度分，分为以下三类：

- 全局锁：锁定数据库中的所有表。

- 表级锁：每次操作锁住整张表。

- 行级锁：每次操作锁住对应的行数据。

## 全局锁

### 介绍

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被阻塞。

其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。

为什么全库逻辑备份，就需要加全就锁呢？

A. 我们一起先来分析一下不加全局锁，可能存在的问题。

假设在数据库中存在这样三张表: tb_stock 库存表，tb_order 订单表，tb_orderlog 订单日志表。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261412961.png)

- 在进行数据备份时，先备份了tb_stock库存表。

- 然后接下来，在业务系统中，执行了下单操作，扣减库存，生成订单（更新tb_stock表，插入tb_order表）。

- 然后再执行备份 tb_order表的逻辑。

- 业务中执行插入订单日志操作。

- 最后，又备份了tb_orderlog表。


此时备份出来的数据，是存在问题的。因为备份出来的数据，tb_stock表与tb_order表的数据不一致(有最新操作的订单信息,但是库存数没减)。

那如何来规避这种问题呢? 此时就可以借助于MySQL的全局锁来解决。

B. 再来分析一下加了全局锁后的情况

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261414111.png)
对数据库进行进行逻辑备份之前，先对整个数据库加上全局锁，一旦加了全局锁之后，其他的DDL、DML全部都处于阻塞状态，但是可以执行DQL语句，也就是处于只读状态，而数据备份就是查询操作。那么数据在进行逻辑备份的过程中，数据库中的数据就是不会发生变化的，这样就保证了数据的一致性和完整性。
### 语法


1). 加全局锁

```sql
flush tables with read lock ;
```

2). 数据备份

```sql
mysqldump -uroot –p1234 itcast > itcast.sql
```

3). 释放锁

```sql
unlock tables ;
```
### 特点

数据库中加全局锁，是一个比较重的操作，存在以下问题：

- 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。

- 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。

在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致性数据备份。

```sql
mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql
```

## 表级锁

### 介绍

表级锁，每次操作锁住整张表。锁定粒度大，发生锁冲突的概率最高，并发度最低。应用在MyISAM、InnoDB、BDB等存储引擎中。

对于表级锁，主要分为以下三类：

- 表锁

- 元数据锁（meta data lock，MDL）

- 意向锁

### 表锁

对于表锁，分为两类：

- 表共享读锁（read lock）

- 表独占写锁（write lock）

**语法**：

加锁：lock tables 表名... read/write。

释放锁：unlock tables / 客户端断开连接 。

**特点**:

A.读锁

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261417941.png)

左侧为客户端一，对指定表加了读锁，不会影响右侧客户端二的读，但是会阻塞右侧客户端的写。

B.写锁

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261419275.png)

左侧为客户端一，对指定表加了写锁，会阻塞右侧客户端的读和写。

>结论: 读锁不会阻塞其他客户端的读，但是会阻塞写。写锁既会阻塞其他客户端的读，又会阻塞其他客户端的写。

### 元数据锁

meta data lock , 元数据锁，简写MDL。

MDL加锁过程是系统自动控制，无需显式使用，在访问一张表的时候会自动加上。MDL锁主要作用是维护表元数据的数据一致性，在表上有活动事务的时候，不可以对元数据进行写入操作。为了避免DML与DDL冲突，保证读写的正确性。

这里的元数据，大家可以简单理解为就是一张表的表结构。 也就是说，某一张表涉及到未提交的事务时，是不能够修改这张表的表结构的。

在MySQL5.5中引入了MDL，当对一张表进行增删改查的时候，加MDL读锁(共享)；当对表结构进行变更操作的时候，加MDL写锁(排他)。

常见的SQL操作时，所添加的元数据锁：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261420583.png)

### 意向锁

为了避免DML在执行时，加的行锁与表锁的冲突，在InnoDB中引入了意向锁，使得表锁不用检查每行数据是否加锁，使用意向锁来减少表锁的检查。

假如没有意向锁，客户端一对表加了行锁后，客户端二如何给表加表锁呢，来通过示意图简单分析一下：

首先客户端一，开启一个事务，然后执行DML操作，在执行DML语句时，会对涉及到的行加行锁。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261422058.png)

当客户端二，想对这张表加表锁时，会检查当前表是否有对应的行锁，如果没有，则添加表锁，此时就会从第一行数据，检查到最后一行数据，效率较低。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261422071.png)

有了意向锁之后 :

客户端一，在执行DML操作时，会对涉及的行加行锁，同时也会对该表加上意向锁。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261422172.png)

而其他客户端，在对这张表加表锁的时候，会根据该表上所加的意向锁来判定是否可以成功加表锁，而不用逐行判断行锁情况了。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261423407.png)

## 行级锁

行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB存储引擎中。

InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁。对于行级锁，主要分为以下三类：

- 行锁（Record Lock）：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261424810.png)

- 间隙锁（Gap Lock）：锁定索引记录间隙（不含该记录），确保索引记录间隙不变，防止其他事务在这个间隙进行insert，产生幻读。在RR隔离级别下都支持。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261424684.png)

- 临键锁（Next-Key Lock）：行锁和间隙锁组合，同时锁住数据，并锁住数据前面的间隙Gap。在RR隔离级别下支持。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261424648.png)

### 行锁

### 介绍

InnoDB实现了以下两种类型的行锁：

- 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。

- 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁。

两种行锁的兼容情况如下:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261425705.png)

常见的SQL语句，在执行时，所加的行锁如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261426162.png)

**演示**

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。

- 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。

- InnoDB的行锁是针对于索引加的锁，不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时 就会升级为表锁。

可以通过以下SQL，查看意向锁及行锁的加锁情况：

```sql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
```

**示例**

数据准备:

```sql
CREATE TABLE `stu` (

`id` int NOT NULL PRIMARY KEY AUTO_INCREMENT,

`name` varchar(255) DEFAULT NULL,

`age` int NOT NULL

) ENGINE = InnoDB CHARACTER SET = utf8mb4;

INSERT INTO `stu` VALUES (1, 'tom', 1);

INSERT INTO `stu` VALUES (3, 'cat', 3);

INSERT INTO `stu` VALUES (8, 'rose', 8);

INSERT INTO `stu` VALUES (11, 'jetty', 11);

INSERT INTO `stu` VALUES (19, 'lily', 19);

INSERT INTO `stu` VALUES (25, 'luci', 25);
```


A. 普通的select语句，执行时，不会加锁。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261428430.png)

B. select...lock in share mode，加共享锁，共享锁与共享锁之间兼容。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261429302.png)
共享锁与排他锁之间互斥。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261429359.png)

客户端一获取的是id为1这行的共享锁，客户端二是可以获取id为3这行的排它锁的，因为不是同一行数据。 而如果客户端二想获取id为1这行的排他锁，会处于阻塞状态，以为共享锁与排他锁之间互斥。

C. 排它锁与排他锁之间互斥

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261430983.png)

当客户端一，执行update语句，会为id为1的记录加排他锁； 客户端二，如果也执行update语句更新id为1的数据，也要为id为1的数据加排他锁，但是客户端二会处于阻塞状态，因为排他锁之间是互斥的。 直到客户端一，把事务提交了，才会把这一行的行锁释放，此时客户端二，解除阻塞。

D. 无索引行锁升级为表锁

stu表中数据如下:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261430602.png)

在两个客户端中执行如下操作:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261430605.png)

在客户端一中，开启事务，并执行update语句，更新name为Lily的数据，也就是id为19的记录 。然后在客户端二中更新id为3的记录，却不能直接执行，会处于阻塞状态，为什么呢？

原因就是因为此时，客户端一，根据name字段进行更新时，name字段是没有索引的，如果没有索引，此时行锁会升级为表锁(因为行锁是对索引项加的锁，而name没有索引)。

接下来，再针对name字段建立索引，索引建立之后，再次做一个测试：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261431583.png)

此时我们可以看到，客户端一，开启事务，然后依然是根据name进行更新。而客户端二，在更新id为3的数据时，更新成功，并未进入阻塞状态。 这样就说明，我们根据索引字段进行更新操作，就可以避免行锁升级为表锁的情况。

### 间隙锁&临键锁

默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。

- 索引上的等值查询(唯一索引)，给不存在的记录加锁时, 优化为间隙锁 。

- 索引上的等值查询(非唯一普通索引)，向右遍历时最后一个值不满足查询需求时，next-keylock 退化为间隙锁。

- 索引上的范围查询(唯一索引)--会访问到不满足条件的第一个值为止。

>注意：间隙锁唯一目的是防止其他事务插入间隙。间隙锁可以共存，一个事务采用的间隙锁不会阻止另一个事务在同一间隙上采用间隙锁。

# InnoDB引擎

## 逻辑存储结构

InnoDB的逻辑存储结构如下图所示。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261434204.png)


1). 表空间

表空间是InnoDB存储引擎逻辑结构的最高层， 如果用户启用了参数 innodb_file_per_table(在8.0版本中默认开启) ，则每张表都会有一个表空间（xxx.ibd），一个mysql实例可以对应多个表空间，用于存储记录、索引等数据。

2). 段

段，分为数据段（Leaf node segment）、索引段（Non-leaf node segment）、回滚段（Rollback segment），InnoDB是索引组织表，数据段就是B+树的叶子节点， 索引段即为B+树的非叶子节点。段用来管理多个Extent（区）。

3). 区

区，表空间的单元结构，每个区的大小为1M。 默认情况下， InnoDB存储引擎页大小为16K， 即一个区中一共有64个连续的页。

4). 页

页，是InnoDB 存储引擎磁盘管理的最小单元，每个页的大小默认为 16KB。为了保证页的连续性，InnoDB 存储引擎每次从磁盘申请 4-5 个区。

5). 行

行，InnoDB 存储引擎数据是按行进行存放的。

在行中，默认有两个隐藏字段：

- trx_id：每次对某条记录进行改动时，都会把对应的事务id赋值给trx_id隐藏列。

- roll_pointer：每次对某条引记录进行改动时，都会把旧的版本写入到undo日志中，然后这个隐藏列就相当于一个指针，可以通过它来找到该记录修改前的信息。
## 架构

### 概述

MySQL5.5 版本开始，默认使用InnoDB存储引擎，它擅长事务处理，具有崩溃恢复特性，在日常开发中使用非常广泛。下面是InnoDB架构图，左侧为内存结构，右侧为磁盘结构。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261438660.png)

### 内存结构

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261438662.png)

在左侧的内存结构中，主要分为这么四大块儿： Buffer Pool、Change Buffer、Adaptive Hash Index、Log Buffer。 接下来介绍一下这四个部分。

**1). Buffer Pool**

InnoDB存储引擎基于磁盘文件存储，访问物理硬盘和在内存中进行访问，速度相差很大，为了尽可能弥补这两者之间的I/O效率的差值，就需要把经常使用的数据加载到缓冲池中，避免每次访问都进行磁盘I/O。

在InnoDB的缓冲池中不仅缓存了索引页和数据页，还包含了undo页、插入缓存、自适应哈希索引以及InnoDB的锁信息等等。

缓冲池 Buffer Pool，是主内存中的一个区域，里面可以缓存磁盘上经常操作的真实数据，在执行增
删改查操作时，先操作缓冲池中的数据（若缓冲池没有数据，则从磁盘加载并缓存），然后再以一定频率刷新到磁盘，从而减少磁盘IO，加快处理速度。

缓冲池以Page页为单位，底层采用链表数据结构管理Page。根据状态，将Page分为三种类型：

- free page：空闲page，未被使用。

- clean page：被使用page，数据没有被修改过。

- dirty page：脏页，被使用page，数据被修改过，也中数据与磁盘的数据产生了不一致。

在专用服务器上，通常将多达80％的物理内存分配给缓冲池 。参数设置： show variables like 'innodb_buffer_pool_size';

**2). Change Buffer**

Change Buffer，更改缓冲区（针对于非唯一二级索引页），在执行DML语句时，如果这些数据Page没有在Buffer Pool中，不会直接操作磁盘，而会将数据变更存在更改缓冲区 Change Buffer中，在未来数据被读取时，再将数据合并恢复到Buffer Pool中，再将合并后的数据刷新到磁盘中。

Change Buffer的意义是什么呢?

先来看一幅图，这个是二级索引的结构图：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261440945.png)

与聚集索引不同，二级索引通常是非唯一的，并且以相对随机的顺序插入二级索引。同样，删除和更新可能会影响索引树中不相邻的二级索引页，如果每一次都操作磁盘，会造成大量的磁盘IO。有了ChangeBuffer之后，我们可以在缓冲池中进行合并处理，减少磁盘IO。


**3). Adaptive Hash Index**

自适应hash索引，用于优化对Buffer Pool数据的查询。MySQL的innoDB引擎中虽然没有直接支持hash索引，但是给我们提供了一个功能就是这个自适应hash索引。因为前面我们讲到过，hash索引在进行等值匹配时，一般性能是要高于B+树的，因为hash索引一般只需要一次IO即可，而B+树，可能需要几次匹配，所以hash索引的效率要高，但是hash索引又不适合做范围查询、模糊匹配等。

InnoDB存储引擎会监控对表上各索引页的查询，如果观察到在特定的条件下hash索引可以提升速度，则建立hash索引，称之为自适应hash索引。

自适应哈希索引，无需人工干预，是系统根据情况自动完成。

参数： adaptive_hash_index

**4). Log Buffer**

Log Buffer：日志缓冲区，用来保存要写入到磁盘中的log日志数据（redo log 、undo log），默认大小为 16MB，日志缓冲区的日志会定期刷新到磁盘中。如果需要更新、插入或删除许多行的事务，增加日志缓冲区的大小可以节省磁盘 I/O。

参数:

innodb_log_buffer_size：缓冲区大小

innodb_flush_log_at_trx_commit：日志刷新到磁盘时机，取值主要包含以下三个：

1: 日志在每次事务提交时写入并刷新到磁盘，默认值。

0: 每秒将日志写入并刷新到磁盘一次。

2: 日志在每次事务提交后写入，并每秒刷新到磁盘一次。

### 磁盘结构

接下来，再来看看InnoDB体系结构的右边部分，也就是磁盘结构：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261443580.png)

**1). System Tablespace**

系统表空间是更改缓冲区的存储区域。如果表是在系统表空间而不是每个表文件或通用表空间中创建的，它也可能包含表和索引数据。(在MySQL5.x版本中还包含InnoDB数据字典、undolog等)

参数：innodb_data_file_path

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261446530.png)

系统表空间，默认的文件名叫 ibdata1。

**2). File-Per-Table Tablespaces**

如果开启了innodb_file_per_table开关 ，则每个表的文件表空间包含单个InnoDB表的数据和索引 ，并存储在文件系统上的单个数据文件中。

开关参数：innodb_file_per_table ，该参数默认开启。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261447823.png)


**3). General Tablespaces**

通用表空间，需要通过 CREATE TABLESPACE 语法创建通用表空间，在创建表时，可以指定该表空间。

A. 创建表空间

```sql
CREATE TABLESPACE ts_name ADD DATAFILE 'file_name' ENGINE = engine_name;
```

B. 创建表时指定表空间

```sql
CREATE TABLE xxx ... TABLESPACE ts_name;
```

**4). Undo Tablespaces**

撤销表空间，MySQL实例在初始化时会自动创建两个默认的undo表空间（初始大小16M），用于存储undo log日志。

**5). Temporary Tablespaces**

InnoDB 使用会话临时表空间和全局临时表空间。存储用户创建的临时表等数据。

**6). Doublewrite Buffer Files**

双写缓冲区，innoDB引擎将数据页从Buffer Pool刷新到磁盘前，先将数据页写入双写缓冲区文件中，便于系统异常时恢复数据。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261449787.png)

**7). Redo Log**

重做日志，是用来实现事务的持久性。该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log）,前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都会存到该日志中, 用于在刷新脏页到磁盘时,发生错误时, 进行数据恢复使用。

以循环方式写入重做日志文件，涉及两个文件：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261450477.png)


前面我们介绍了InnoDB的内存结构，以及磁盘结构，那么内存中我们所更新的数据，又是如何到磁盘中的呢？ 此时，就涉及到一组后台线程，接下来，就来介绍一些InnoDB中涉及到的后台线程。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261451689.png)

### 后台线程

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261503235.png)

**1). Master Thread**

核心后台线程，负责调度其他线程，还负责将缓冲池中的数据异步刷新到磁盘中, 保持数据的一致性，还包括脏页的刷新、合并插入缓存、undo页的回收 。

**2). IO Thread**

在InnoDB存储引擎中大量使用了AIO来处理IO请求, 这样可以极大地提高数据库的性能，而IO Thread主要负责这些IO请求的回调。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261507877.png)

可以通过以下的这条指令，查看到InnoDB的状态信息，其中就包含IO Thread信息。

```sql
show engine innodb status \G;
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261508772.png)

**3). Purge Thread**

主要用于回收事务已经提交了的undo log，在事务提交之后，undo log可能不用了，就用它来回收。

**4). Page Cleaner Thread**

协助 Master Thread 刷新脏页到磁盘的线程，它可以减轻 Master Thread 的工作压力，减少阻塞。

## 事务原理

### 事务基础

1). 事务

事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败。

2). 特性

• 原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。

• 一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。

• 隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。

• 持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。

### 并发事务问题

1). 赃读：一个事务读到另外一个事务还没有提交的数据。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261513888.png)

2). 不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261513489.png)

3). 幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了 "幻影"。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261513866.png)

### 事务隔离级别

为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。主要有以下几种：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261514338.png)

1). 查看事务隔离级别

```sql
SELECT @@TRANSACTION_ISOLATION;
```

2). 设置事务隔离级别

```sql
SET [ SESSION | GLOBAL ] TRANSACTION ISOLATION LEVEL { READ UNCOMMITTED | READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
```

>注意：事务隔离级别越高，数据越安全，但是性能越低。

### 原理

那实际上，我们研究事务的原理，就是研究MySQL的InnoDB引擎是如何保证事务的这四大特性的。

而对于这四大特性，实际上分为两个部分。 其中的原子性、一致性、持久化，实际上是由InnoDB中的两份日志来保证的，一份是redo log日志，一份是undo log日志。 而持久性是通过数据库的锁，加上MVCC来保证的。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261510499.png)
### redo log

重做日志，记录的是事务提交时数据页的物理修改，是用来实现事务的持久性。

该日志文件由两部分组成：重做日志缓冲（redo log buffer）以及重做日志文件（redo log file）,前者是在内存中，后者在磁盘中。当事务提交之后会把所有修改信息都存到该日志文件中, 用于在刷新脏页到磁盘,发生错误时, 进行数据恢复使用。

如果没有redolog，可能会存在什么问题的？ 我们一起来分析一下。

在InnoDB引擎中的内存结构中，主要的内存区域就是缓冲池，在缓冲池中缓存了很多的数据页。 当我们在一个事务中，执行多个增删改的操作时，InnoDB引擎会先操作缓冲池中的数据，如果缓冲区没有对应的数据，会通过后台线程将磁盘中的数据加载出来，存放在缓冲区中，然后将缓冲池中的数据修改，修改后的数据页我们称为脏页。 而脏页则会在一定的时机，通过后台线程刷新到磁盘
中，从而保证缓冲区与磁盘的数据一致。 而缓冲区的脏页数据并不是实时刷新的，而是一段时间之后将缓冲区的数据刷新到磁盘中，假如刷新到磁盘的过程出错了，而提示给用户事务提交成功，而数据却没有持久化下来，这就出现问题了，没有保证事务的持久性。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261516921.png)

如何解决上述的问题呢？ 在InnoDB中提供了一份日志 redo log，接下来我们再来分析一下，通过redolog如何解决这个问题。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261517435.png)

有了redolog之后，当对缓冲区的数据进行增删改之后，会首先将操作的数据页的变化，记录在redo log buffer中。在事务提交时，会将redo log buffer中的数据刷新到redo log磁盘文件中。过一段时间之后，如果刷新缓冲区的脏页到磁盘时，发生错误，此时就可以借助于redo log进行数据恢复，这样就保证了事务的持久性。 而如果脏页成功刷新到磁盘 或 或者涉及到的数据已经落盘，此时redolog就没有作用了，就可以删除了，所以存在的两个redolog文件是循环写的。

那为什么每一次提交事务，要刷新redo log 到磁盘中呢，而不是直接将buffer pool中的脏页刷新
到磁盘呢 ?因为在业务操作中，我们操作数据一般都是随机读写磁盘的，而不是顺序读写磁盘。 而redo log在往磁盘文件中写入数据，由于是日志文件，所以都是顺序写的。顺序写的效率，要远大于随机写。 这种先写日志的方式，称之为 WAL（Write-Ahead Logging）。
### undo log

回滚日志，用于记录数据被修改前的信息 , 作用包含两个 : 提供回滚(保证事务的原子性) 和MVCC(多版本并发控制) 。

undo log和redo log记录物理日志不一样，它是逻辑日志。可以认为当delete一条记录时，undo log中会记录一条对应的insert记录，反之亦然，当update一条记录时，它记录一条对应相反的update记录。当执行rollback时，就可以从undo log中的逻辑记录读取到相应的内容并进行回滚。

Undo log销毁：undo log在事务执行时产生，事务提交时，并不会立即删除undo log，因为这些日志可能还用于MVCC。

Undo log存储：undo log采用段的方式进行管理和记录，存放在前面介绍的 rollback segment回滚段中，内部包含1024个undo log segment。

## MVCC

### 基本概念

**1). 当前读**

读取的是记录的最新版本，读取时还要保证其他并发事务不能修改当前记录，会对读取的记录进行加锁。对于我们日常的操作，如：select ... lock in share mode(共享锁)，select ... for update、update、insert、delete(排他锁)都是一种当前读。

测试：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261519754.png)

在测试中我们可以看到，即使是在默认的RR隔离级别下，事务A中依然可以读取到事务B最新提交的内容，因为在查询语句后面加上了 lock in share mode 共享锁，此时是当前读操作。当然，当我们加排他锁的时候，也是当前读操作。

**2). 快照读**

简单的select（不加锁）就是快照读，快照读，读取的是记录数据的可见版本，有可能是历史数据，不加锁，是非阻塞读。

• Read Committed：每次select，都生成一个快照读。

• Repeatable Read：开启事务后第一个select语句才是快照读的地方。

• Serializable：快照读会退化为当前读。

测试:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261520407.png)

在测试中,我们看到即使事务B提交了数据,事务A中也查询不到。 原因就是因为普通的select是快照读，而在当前默认的RR隔离级别下，开启事务后第一个select语句才是快照读的地方，后面执行相同的select语句都是从快照中获取数据，可能不是当前的最新数据，这样也就保证了可重复读。

**3). MVCC**

全称 Multi-Version Concurrency Control，多版本并发控制。指维护一个数据的多个版本，使得读写操作没有冲突，快照读为MySQL实现MVCC提供了一个非阻塞读功能。MVCC的具体实现，还需要依赖于数据库记录中的三个隐式字段、undo log日志、readView。

接下来，介绍一下InnoDB引擎的表中涉及到的隐藏字段 、undolog 以及 readview，从而来介绍一下MVCC的原理。

### 隐藏字段

#### 介绍

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261521313.png)

当创建了上面的这张表，我们在查看表结构的时候，就可以显式的看到这三个字段。 实际上除了这三个字段以外，InnoDB还会自动的给我们添加三个隐藏字段及其含义分别是：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261522737.png)

而上述的前两个字段是肯定会添加的， 是否添加最后一个字段DB_ROW_ID，得看当前表有没有主键，如果有主键，则不会添加该隐藏字段。

### undolog

#### 介绍

回滚日志，在insert、update、delete的时候产生的便于数据回滚的日志。当insert的时候，产生的undo log日志只在回滚时需要，在事务提交后，可被立即删除。而update、delete的时候，产生的undo log日志不仅在回滚时需要，在快照读时也需要，不会立即被删除。

#### 版本链

有一张表原始数据为：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261523009.png)

>DB_TRX_ID : 代表最近修改事务ID，记录插入这条记录或最后一次修改该记录的事务ID，是自增的。
>
>DB_ROLL_PTR ： 由于这条数据是才插入的，没有被更新过，所以该字段值为null。

然后，有四个并发事务同时在访问这张表。


A. 第一步

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261524559.png)

当事务2执行第一条修改语句时，会记录undo log日志，记录数据变更之前的样子; 然后更新记录，并且记录本次操作的事务ID，回滚指针，回滚指针用来指定如果发生回滚，回滚到哪一个版本。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261524949.png)

B.第二步

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261525640.png)

当事务3执行第一条修改语句时，也会记录undo log日志，记录数据变更之前的样子; 然后更新记录，并且记录本次操作的事务ID，回滚指针，回滚指针用来指定如果发生回滚，回滚到哪一个版本。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261525176.png)

C. 第三步

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261525798.png)

当事务4执行第一条修改语句时，也会记录undo log日志，记录数据变更之前的样子; 然后更新记
录，并且记录本次操作的事务ID，回滚指针，回滚指针用来指定如果发生回滚，回滚到哪一个版本。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261526957.png)

>最终发现，不同事务或相同事务对同一条记录进行修改，会导致该记录的undolog生成一条记录版本链表，链表的头部是最新的旧记录，链表尾部是最早的旧记录。


### readview

ReadView（读视图）是 快照读 SQL执行时MVCC提取数据的依据，记录并维护系统当前活跃的事务（未提交的）id。

ReadView中包含了四个核心字段：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261526151.png)

而在readview中就规定了版本链数据的访问规则：trx_id 代表当前undolog版本链对应事务ID。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261527011.png)

不同的隔离级别，生成ReadView的时机不同：

- READ COMMITTED ：在事务中每一次执行快照读时生成ReadView。

- REPEATABLE READ：仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。

### 原理分析

#### RC隔离级别

RC隔离级别下，在事务中每一次执行快照读时生成ReadView。

我们就来分析事务5中，两次快照读读取数据，是如何获取数据的?

在事务5中，查询了两次id为30的记录，由于隔离级别为Read Committed，所以每一次进行快照读都会生成一个ReadView，那么两次生成的ReadView如下。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261527444.png)
那么这两次快照读在获取数据时，就需要根据所生成的ReadView以及ReadView的版本链访问规则，到undolog版本链中匹配数据，最终决定此次快照读返回的数据。

A. 先来看第一次快照读具体的读取过程：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261528025.png)

在进行匹配时，会从undo log的版本链，从上到下进行挨个匹配：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261535975.png)

B. 再来看第二次快照读具体的读取过程:

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261530954.png)
在进行匹配时，会从undo log的版本链，从上到下进行挨个匹配：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261530547.png)

#### RR隔离级别

RR隔离级别下，仅在事务中第一次执行快照读时生成ReadView，后续复用该ReadView。 而RR 是可重复读，在一个事务中，执行两次相同的select语句，查询到的结果是一样的。那MySQL是如何做到可重复读的呢? 我们简单分析一下就知道了。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261531421.png)

我们看到，在RR隔离级别下，只是在事务中第一次快照读时生成ReadView，后续都是复用该ReadView，那么既然ReadView都一样， ReadView的版本链匹配规则也一样， 那么最终快照读返回的结果也是一样的。

所以呢，MVCC的实现原理就是通过 InnoDB表的隐藏字段、UndoLog 版本链、ReadView来实现的。而MVCC + 锁，则实现了事务的隔离性。 而一致性则是由redolog 与 undolog保证。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403261532145.png)

