---
title: vue&elementUI使用总结
tags:
  - vue
  - elementUI
  - axios
  - router
categories:
  - 前端
keywords: vue
index_img: /img/vue/vue.png
excerpt: 一个流行的开源JavaScript前端框架，用于构建现代化的用户界面和单页面应用（SPA），它注重易用性、灵活性和性能，提供了数据驱动的视图组件、双向数据绑定和简洁的语法，使开发者能够快速开发交互性强、响应迅速的Web应用。
abbrlink: 888f2d51
date: 2020-10-09 20:41:26
---
> 由于工作需要，最近简单使用了vue参与了前端的开发工作，因为有Android开发经验，稍微补充了前端知识后，已经可以应对基础的后台管理项目需求开发了，基本掌握了vue的用法、element UI组件、路由组件、axios等。当然，目前是前端初学者，虽然只看了不到一周，至少能参与公司内部的后台管理前端开发需求了，还挺有自豪感的。简单总结下。

---

Vues一款用于构建用户界面的渐进式的JavaScript框架。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151728471.png)

# 快速入门

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151729541.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151729479.png)

# 常用指令

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151730577.png)

## v-for

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151730170.png)

## v-bind

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151730509.png)

## v-if&v-show

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151731436.png)

## v-on

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151731708.png)

## v-model

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151732325.png)

# vue生命周期

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151732617.png)

# Axios

Axios对原生Ajax进行了封装，简化书写，快速开发。

官网: https://www.axios-http.cn/

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151734525.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151734234.png)

# Vue项目创建

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151735996.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151735533.png)

然后通过 `code .` 通过vscode打开当前项目。

# vue项目结构

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151736513.png)

# 项目启动

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151736457.png)

# Vue开发流程

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151737741.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151737491.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151737613.png)

**重点就是编写.vue文件。**

# API 风格

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151738494.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151738149.png)

# Element Plus

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151739356.png)

官网： https://element-plus.org/zh-CN/#/zh-CN

这个就是一个前端UI组件库，看菜做饭，需要什么直接拿来用，调整成需求需要的形式即可。

# router组件

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151740663.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151740061.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151740956.png)

# Pinia状态管理库

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151741388.png)

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151741400.png)


# Pinia持久化插件-persist

网页一刷新，就不再携带Authorization，就会报错。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403151742534.png)

