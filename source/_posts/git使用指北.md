---
title: git使用指北
tags:
  - git
categories:
  - git
keywords: git
index_img: /img/git/git.png
excerpt: 软件开发必备~
abbrlink: 2581ce52
date: 2019-10-18 21:06:13
---
> 日常工作中，git操作不可或缺，一些常用命令已经充分熟练使用，不过一些配置还是需要用到的时候在看一眼，所幸就自己记录下来，再过一遍，加深印象。

---

# 前言

## 什么是git

Git（读音为/gɪt/）是一个开源的分布式版本控制系统，可以有效、高速地处理从很小到非常大的项目版本管理。也是Linus Torvalds为了帮助管理Linux内核开发而开发的一个开放源码的版本控制软件。

## 使用git能做什么

代码回溯：Git在管理文件过程中会记录日志，方便回退到历史版本。
版本切换：Git存在分支的概念，一个项目可以有多个分支（版本），可以任意切换。
多人协作：Git支持多人协作，即一个团队共同开发一个项目，每个团队成员负责一部分代码，通过Git就可以管理和协调。
远程备份：Git通过仓库管理文件，在Git中存在远程仓库，如果本地文件丢失还可以从远程仓库获取。

## git常用命令汇总

git status	查看文件状态
git add 文件名称	将文件的修改加入暂存区
①git reset 文件名	将暂存区的文件取消暂存
②git reset --hard “版本号”	将暂存区的文件切换到指定版本
git commit -m “备注信息” 文件名	将暂存区的文件修改提交到版本库
git log	查看日志
git remote	查看远程仓库
git remote add 远程仓库简称 远程仓库地址	把本地仓库添加到远程
git clone “远程仓库地址”	从远程仓库克隆
git push 远程仓库简称 分支名称	将分支推送到远程
git pull	从远程仓库拉取
git branch	列出所有本地分支
git branch -r	列出所有远程分支
git branch -a	列出所有本地分支和远程分支
git checkout 分支名称	切换分支
git merge 分支名称	将两个分支文件进行合并
git tag	查看标签
git tag 标签名称	创建标签
git push origin 分支名称	将标签推送到远程仓库
git checkout -b 分支名 标签名	检出标签

# git概述

## git简介

Git 是一个分布式版本控制工具，通常用来对软件开发过程中的源代码文件进行管理。通过Git 仓库来存储和管理这些文件，Git仓库分为两种：

- 本地仓库：开发人员自己电脑上的 Git 仓库
- 远程仓库：远程服务器上的 Git 仓库
- 
![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151114525.png)

commit：提交,将本地文件和版本信息保存到本地仓库  
push：推送,将本地仓库文件和版本信息上传到远程仓库  
pull：拉取,将远程仓库文件和版本信息下载到本地仓库

# git常用命令

## git全局设置

当安装Git后首先要做的事情是设置用户名称和email地址。这是非常重要的，因为每次Git提交都会使用该用户信息。在Git 命令行中执行下面命令：  

设置用户信息

```
git config --global user.name "aaa" 
git config --global user.email "aaa.email"
```

查看配置信息

```
git config --list
```


## 获取git仓库

要使用Git对我们的代码进行管理，首先需要获得Git仓库。  
获取Git仓库通常有两种方式：  
- 在本地初始化Git仓库（不常用）  
- 从远程仓库克隆（常用）

### 在本地初始化仓库

操作步骤如下：  
在任意目录下创建一个空目录（例如gitTest）作为我们的本地Git仓库  
进入这个目录中，点击右键打开Git bash窗口  

执行命令

```
git init
```

如果在当前目录中看到.git文件夹（此文件夹为隐藏文件夹）则说明Git仓库创建成功

### 从远程仓库克隆

可以通过Git提供的命令从远程仓库进行克隆，将远程仓库克隆到本地  
命令格式：git clone 远程仓库地址

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151117285.png)

## 工作区、暂存区、版本库

为了更好的学习Git，我们需要了解Git相关的一些概念，这些概念在后面的学习中会经常提到。
**版本库**：前面看到的.git隐藏文件夹就是版本库，版本库中存储了很多配置信息、日志信息和文件版本信息等
**工作区**：包含.git文件夹的目录就是工作区，也称为工作目录，主要用于存放开发的代码
**暂存区**：.git文件夹中有很多文件，其中有一个index文件就是暂存区，也可以叫做stage。暂存区是一个临时保存修改文件的地方。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151118350.png)


## git工作区中文件的状态

Git工作区中的文件存在两种状态：
untracked 未跟踪（未被纳入版本控制）：文件是新创建的，git没有去管理它
tracked 已跟踪（被纳入版本控制）
（1）Unmodified 未修改状态
（2）Modified 已修改状态
（3）Staged 已暂存状态

注意：文件的状态会随着我们执行Git的命令发生变化

## 本地仓库操作

### git status

此时远程克隆仓库的文件状态（我们新建了user.java与user.xml文件）：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151120980.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151120790.png)

### git add

git add 命令的作用是将文件的修改加入暂存区。  
命令格式：git add fileName  
加入暂存区后再执行 git status 命令，可以发现文件的状态已经发生变化。  
执行add命令后查看状态：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151121480.png)

### 取消暂存

git reset 命令的作用是将暂存区的文件取消暂存或者是切换到指定版本  
取消暂存命令格式：`git reset 文件名`

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151122824.png)

### git commit

git commit 命令的作用是将暂存区的文件修改提交到版本库  
命令格式：git commit -m “备注信息” 文件名  
提交了user.java文件之后，就只剩下了一个未暂存的user.xml

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151123519.png)

### git log

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151124372.png)

通过git log命令查看日志，可以发现每次提交都会产生一个版本号，提交时设置的message、提交人、邮箱、提交时间等信息都会记录到日志中

### 切换版本

每次提交到版本库，都会生成对应的版本号，可以根据版本号切换到指定版本。

切换到指定版本命令格式：`git reset --hard 版本号`

## 远程仓库操作

### git remote

如果要查看已经配置的远程仓库服务器，可以执行 git remote 命令，它会列出每一个远程服务器的简称。  
如果已经克隆了远程仓库， 至少能够看到origin，这是Git克隆的仓库服务器的默认名字。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151127586.png)

### git remote add

添加远程仓库命令格式：git remote add 简称 远程仓库地址  
把本地仓库添加到远程，远程仓库的默认名称为origin

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151127673.png)

### git clone

就是克隆远端仓库。

### git push

将本地仓库内容推送到远程仓库。  
命令格式：`git push 远程仓库简称 分支名称`

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151128020.png)

### git pull

git pull 命令的作用是从远程仓库获取最新版本并合并到本地仓库
命令格式：git pull 远程仓库简称 分支名称
在本地初始化一个新的repo仓库，并在此仓库中创建new.java文件。然后把此仓库添加到远程仓库（远程仓库依然是我们上文中用到的ceshi仓库）。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151129048.png)


## 分支操作

分支是Git 使用过程中非常重要的概念。使用分支意味着你可以把你的工作从开发主线上分离开来，以免影响开发主线。  
本地仓库和远程仓库中都有分支，同一个仓库可以有多个分支，各个分支相互独立，互不干扰。  
通过git init 命令创建本地仓库时默认会创建一个master分支。

### 查看分支

查看分支命令：git branch  
git branch 列出所有本地分支  
git branch -r 列出所有远程分支  
git branch -a 列出所有本地分支和远程分支  
此时再回到我们的ceshi文件夹（也就是从远程拉取下来的ceshi仓库）

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151129272.png)


### 创建分支

创建分支命令格式：`git branch 分支名称`

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151130249.png)

### 切换分支

一个仓库中可以有多个分支。  
切换分支命令格式：`git checkout 分支名称`

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151130374.png)

### 推送至远程仓库分支

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151131849.png)

### 合并分支

合并分支就是将两个分支的文件进行合并处理。  
令格式：`git merge 分支名称  ``
此时我们把user.java中的文件修改为(此时我们已经切换到b1分支)并把b1分支提交推送至远程仓库:

如果要做的是把b1合并到master，需要先切换到master分支

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151132755.png)


## 标签

Git 中的标签，指的是某个分支某个特定时间点的状态。通过标签，可以很方便的切换到标记时的状态。

### 查看标签

查看标签命令：`git tag`

### 创建标签

命令格式: `git tag 标签名称`

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151133424.png)

### 将标签推送到远程仓库

命令格式：`git push origin 标签名称`

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151134365.png)

### 检出标签

检出标签时需要新建一个分支来指向某个标签，  
检出标签的命令格式：`git checkout -b 分支名 标签名  `
标签完成之后，就不会再修改了。

检出标签就是在这个标签的基础上进行其他的开发或操作。  
检出标签的操作实质 ： 就是以标签指定的版本为基础版本，新建一个分支，继续其他的操作。因此 ，就是新建分支的操作了。

