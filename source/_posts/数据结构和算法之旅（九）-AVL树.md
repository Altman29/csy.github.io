---
title: 数据结构和算法之旅（九）- AVL树
excerpt: AVL树开始了，它是平衡二叉树的一种哦~。通过旋转操作保持树的平衡，确保查找、插入和删除操作的时间复杂度都是O(log n)，适用于高效的数据组织和搜索。
abbrlink: cbf454fc
date: 2018-12-11 13:52:13
tags:
  - AVL树
categories:
  - data&algorithm
keywords: AVL树;平衡二叉树;
index_img: /img/data_math/index.png
---

<p class="note note-light">
二叉搜索树，如果不平衡，那么查找效率就会从对数级降级成O(n)。<br>
可以通过旋转来恢复平衡，而且旋转并不影响二叉搜索树的特性。<br>
那么，如何判断是否不平衡呢，是有个结论的：<br>
如果一个节点的左右孩子，高度差超过1，则此节点失衡，才需要旋转。
</p>


![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309212107467.png)


![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202309212109106.png)


## 定义

> - 二叉搜索树在插入和删除时，节点可能失衡。
> - 如果在插入和删除时通过旋转，始终让二叉搜索树保持平衡，称为自平衡的二叉搜索树。
> - AVL是自平衡二叉搜索树的实现之一。