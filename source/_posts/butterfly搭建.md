---
title: butterfly搭建
abbrlink: '21e21024'
date: 2023-09-14 16:51:06
tags:
sticky: 2
---



## post front-matter 文章页配置

```yaml
---
title:					【必需】文章标题
date:					【必需】文章创建日期
updated:				【可选】文章更新日期
tags:					【可选】文章标签
categories:				【可选】文章分类
keywords:				【可选】文章关键字
description:			【可选】文章描述
top_img:				【可选】文章顶部图片
comments:				【可选】显示文章评论模块(默认 true)
cover:					【可选】文章缩略图(如果没top_img,顶部将显示缩略图，可设为false/图片地址/留空)
copyright:				【可选】显示文章版权模块(默认为设置中post_copyright的enable配置)
copyright_author:		【可选】文章版权模块的文章作者
copyright_author_href:	【可选】文章版权模块的文章作者链接
copyright_url:			【可选】文章版权模块的文章连结链接
copyright_info:			【可选】文章版权模块的版权声明文字
highlight_shrink:		【可选】配置代码框是否展开(true/false)(默认为设置中highlight_shrink的配置)
aside:					【可选】显示侧边栏 (默认 true)
---
```

## page front-matter 页面配置

```yaml
---
title:				【必需】页面标题
date:				【必需】页面创建日期
updated:			【可选】页面更新日期
type:				【必需】标签、分类和友情链接三个页面需要配置
comments:			【可选】显示页面评论模块(默认 true)
description:		【可选】页面描述
keywords:			【可选】页面关键字
top_img:			【可选】页面顶部图片
aside:				【可选】显示侧边栏 (默认 true)
highlight_shrink:	【可选】配置代码框是否展开(true/false)(默认为设置中highlight_shrink的配置)
---
```



## 导航栏

修改主题配置文件_config.butterfly.yml

```yaml
menu:
  首页: / || fas fa-home
  时间轴: /archives/ || fas fa-archive
  标签: /tags/ || fas fa-tags
  分类: /categories/ || fas fa-folder-open
  清单||fa fa-heartbeat:
    音乐: /music/ || fas fa-music
    照片: /Gallery/ || fas fa-images
    电影: /movies/ || fas fa-video
  友链: /link/ || fas fa-link
  关于: /about/ || fas fa-heart
```





