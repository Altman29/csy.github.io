---
title: 数据结构和算法之旅（十一）-B、B+树
excerpt: B树和B+树是多路搜索树，B树中非叶子节点存储数据，适用于数据库索引，B+树则将数据全部存储在叶子节点上，适用于范围查询和外部存储，两者都具备平衡和高效的插入删除特性。
abbrlink: c24ce775
date: 2019-01-08 19:27:12
tags:
  - B树
categories:
  - data&algorithm
keywords: B树
index_img: /img/data_math/index.png
---

> B树也是一种自平衡的树形数据结构，它可以存储大量的数据并且支持高效的查找、插入、删除操作。前面所涉及的AVL树、红黑树也是自平衡的，也很高效都能达到对数级别，为什么还有学习B树呢。因为AVL、红黑树是适用于内存中的，而B树是用于解决磁盘存储上的数据管理问题的。B树设计的目标就是减少磁盘I/O操作的次数，从而提高数据访问到效率。
> 
> 而B+树时候后来引入的B树的变种，在B树的基础上做了一些改进，使得它更适合数据库索引和文件系统的应用，B+树在范围查询和顺序遍历等操作上具有更好的性能。
> 
> 后来，随着非关系型数据库(NoSQL数据库)的兴起，B树和B+树仍然在各种数据库系统中发挥重要作用。此外，B树的变种，如B* 树，B#树，也被提出来以满足不同应用场景的需求。 



## why B Tree

> 之前的AVL数、红黑树，它们的效率取决于树的高度。
> 树的高度越低，效率越高；树的高度越高，效率越低。
> 
> 如果100万数据使用AVL树存储，树的高度是多少？
> 大约是20的高度。在内存中很快，可以接受。
> 如果是磁盘读写，如果100万数据用AVL，要做20次磁盘的读写，是很慢的，人是能感觉出来的。
> 
> 如果是100万数据，存储到B树中（最小度数是500），树的高度是多少？
> 大概是3！

<p class="note note-success">
结论：100万的数据，
AVL树大概树高是20；
B树大概是3；
</p>

## 度和阶

> 要先了解俩个定义：
> 度 degree 指树中节点孩子树
> 阶 order 指所有节点孩子数最大值

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309222135905.png)

例如上图：
**度 degree**
- 4有俩个孩子，它的度数是2；
- 2也有俩个孩子，它的度数是2；
- 1和3这俩个叶子节点，没有孩子，度数是0；
- 6 8这个节点，有三个孩子，它的度数是3；

**阶 order**
- 6 8是所有孩子节点最多的，有三个孩子，称整个B树阶为3；

## B树特性

<p class="note note-info">
1.每个节点最多有m个孩子，其中m称为B树的阶；<br>
2.除根节点和叶子节点外，其他每个节点至少有`ceil(m/2)`个孩子（ceil是向上取整）；<br>
3.若根节点不是叶子节点，则至少有俩个孩子；<br>
4.所有叶子节点都在同一层；<br>
5.每个非叶子节点由n个关键字和n+1个指针组成，其中`ceil(m/2)-1<=n<=m-1`;<br>
6.关键字按非降序排列，即节点中的第i个关键字大于等于第i-1个关键字；<br>
7.指针P[i]指向关键字值位于第i个关键字和第i+1个关键字之间的子树；
</p>

<p class="note note-success">
其中第5点：
比如"6 8"节点有俩个关键字，就有3个指针指向3个子节点。<br>
就是一个key有俩孩子；<br>
俩个key有仨孩子；<br>
其中ceil(m/2)-1<=n<=m-1意思是：<br>
key的个数也有个范围，要比最小的ceil(m/2)-1大，要比阶数-1小；<br>
孩子数： ceil(m/2)-1 ~ m<br>
关键字数：ceil(m/2)-1 ~ m-1<br>
其中第6，7点：<br>
总结就是升序排列；
</p>

## 分析

### 多路查找

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309222202785.png)

如图所示：
根节点由5个key，那么就有6个孩子。这种情况下，如何查找呢？

举个例子：
比如，查找20，那么就在当前key数组中找，5,10,15,20就找到了。
比如，查找21，比21小的就不用看了，所以去找20到25直接的子节点去查找，继续一次查找，找到了就返回，找不到继续向符合的子节点找，直到找到叶子节点仍然没有说明针对没有了。

### 新增

看几个新增示例，最大keynumber是3，来理解新增的过程。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309231506137.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309231506163.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309231507304.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309231507560.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309231508760.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309231508972.png)

遇到叶子节点，直接加入，如果达到key的最大值，需要分裂重新排列。

<p class="note note-success">
- 首先查找本节点中插入位置i，如果没有空位(key被找到)，应该走新增逻辑，<br>
- 接下来分俩种情况：<br>
	- 如果节点是叶子节点，可以直接插入<br>
	- 如果节点是非叶子节点，需要继续在children[i]处继续递归插入<br>
- 无论哪种情况，插入完成后都可能超过节点keys数目限制，此时应该执行节点分裂。
</p>

<p class="note note-info">
分裂split的逻辑其实很简单，可以总结为：<br>
- 创建right节点（分裂后大于当前left节点的，left为待分裂节点），把t以后的key和child都拷贝过去。<br>
- t-1处的key（中间的key）插入到parent的index处，index指的是left作为孩子时的索引。<br>
- right索引作为parent的孩子插入到index+1出。<br>
- 如果parent==null表示要分裂的是根节点，此时需要创建一个新根，原来的根节点作为新根的0孩子<br>
- 否则：<br>
	- 创建right节点（分裂后大于等于当前left节点），把t以后的key和child都拷贝过去<br>
	- t-1处的key插入到parent的index处，index指的是left作为孩子时的索引<br>
	- right节点作为parent的孩子插入到index+1出<br>
</p>


### 删除


## code

