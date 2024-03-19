---
title: elasticseatch-安装小记
tags:
  - AMQP
categories:
  - 微服务
keywords: AMQP
index_img: /img/micro/elk.jpg
excerpt: elasticsearch是一款强大的分布式搜索引擎，相关安装指南记录一下。
abbrlink: 8e215a30
date: 2021-09-04 15:35:58
---
# 部署单点ES

## 创建网络

因为我们还需要部署kibana容器，因此需要让es和kibana容器互联。这里先创建一个网络：

```bash
docker network create es-net
```

## 加载镜像

这里我们采用elasticsearch的7.12.1版本的镜像，这个镜像体积非常大，接近1G。

将其上传到虚拟机中，然后运行命令加载即可

```bash
# 导入数据
docker load -i es.tar
```

同理还有`kibana`的tar包也需要这样做。

## 运行

运行docker命令，部署单点es：

```bash
docker run -d \
	--name es \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
    -e "discovery.type=single-node" \
    -v es-data:/usr/share/elasticsearch/data \
    -v es-plugins:/usr/share/elasticsearch/plugins \
    --privileged \
    --network es-net \
    -p 9200:9200 \
    -p 9300:9300 \
elasticsearch:7.12.1
```

命令解释：

- `-e "cluster.name=es-docker-cluster"`：设置集群名称
- `-e "http.host=0.0.0.0"`：监听的地址，可以外网访问
- `-e "ES_JAVA_OPTS=-Xms512m -Xmx512m"`：内存大小
- `-e "discovery.type=single-node"`：非集群模式
- `-v es-data:/usr/share/elasticsearch/data`：挂载逻辑卷，绑定es的数据目录
- `-v es-logs:/usr/share/elasticsearch/logs`：挂载逻辑卷，绑定es的日志目录
- `-v es-plugins:/usr/share/elasticsearch/plugins`：挂载逻辑卷，绑定es的插件目录
- `--privileged`：授予逻辑卷访问权
- `--network es-net` ：加入一个名为es-net的网络中
- `-p 9200:9200`：端口映射配置

在浏览器中输入：[http://192.168.150.101:9200](http://192.168.150.101:9200) 即可看到elasticsearch的响应结果：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191330169.png)

# 部署kibana

kibana可以给我们提供一个elasticsearch的可视化界面，便于我们学习。

## 部署

运行docker命令，部署kibana

```sh
docker run -d \
--name kibana \
-e ELASTICSEARCH_HOSTS=http://es:9200 \
--network=es-net \
-p 5601:5601  \
kibana:7.12.1
```

- `--network es-net` ：加入一个名为es-net的网络中，与elasticsearch在同一个网络中
- `-e ELASTICSEARCH_HOSTS=http://es:9200"`：设置elasticsearch的地址，因为kibana已经与elasticsearch在一个网络，因此可以用容器名直接访问elasticsearch
- `-p 5601:5601`：端口映射配置

kibana启动一般比较慢，需要多等待一会，可以通过命令：

```bash
docker logs -f kibana
```

查看运行日志，当查看到下面的日志，说明成功：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191331082.png)

此时，在浏览器输入地址访问：[http://192.168.150.101:5601](http://192.168.150.101:5601)，即可看到结果
## DevTools

kibana中提供了一个DevTools界面：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191331490.png)

这个界面中可以编写DSL来操作elasticsearch。并且对DSL语句有自动补全功能。

# 安装IK分词器

## 在线安装ik插件

```sh
# 进入容器内部
docker exec -it elasticsearch /bin/bash

# 在线下载并安装
./bin/elasticsearch-plugin  install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip

#退出
exit
#重启容器
docker restart elasticsearch
```

## 离线安装ik插件（推荐）

### 查看数据卷目录

安装插件需要知道elasticsearch的plugins目录位置，而我们用了数据卷挂载，因此需要查看elasticsearch的数据卷目录，通过下面命令查看:

```sh
docker volume inspect es-plugins
```

显示结果：

```sh
[
    {
        "CreatedAt": "2022-05-06T10:06:34+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/es-plugins/_data",
        "Name": "es-plugins",
        "Options": null,
        "Scope": "local"
    }
]
```

说明plugins目录被挂载到了：`/var/lib/docker/volumes/es-plugins/_data`这个目录中。

### 解压分词器安装包

把的ik分词器解压缩，重命名为ik。
### 上传到es容器的插件数据卷中

也就是`/var/lib/docker/volumes/es-plugins/_data`：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191333243.png)
### 重启容器

```sh
# 4、重启容器
docker restart es

# 查看es日志
docker logs -f es
```

### 测试

IK分词器包含两种模式：

- `ik_smart`：最少切分
- `ik_max_word`：最细切分

```json
GET /_analyze
{
  "analyzer": "ik_max_word",
  "text": "java程序员学习太棒了"
}
```

结果：

```json
{
  "tokens" : [
    {
      "token" : "程序员",
      "start_offset" : 2,
      "end_offset" : 5,
      "type" : "CN_WORD",
      "position" : 1
    },
    {
      "token" : "程序",
      "start_offset" : 2,
      "end_offset" : 4,
      "type" : "CN_WORD",
      "position" : 2
    },
    {
      "token" : "员",
      "start_offset" : 4,
      "end_offset" : 5,
      "type" : "CN_CHAR",
      "position" : 3
    },
    {
      "token" : "学习",
      "start_offset" : 5,
      "end_offset" : 7,
      "type" : "CN_WORD",
      "position" : 4
    },
    {
      "token" : "java",
      "start_offset" : 7,
      "end_offset" : 11,
      "type" : "ENGLISH",
      "position" : 5
    },
    {
      "token" : "太棒了",
      "start_offset" : 11,
      "end_offset" : 14,
      "type" : "CN_WORD",
      "position" : 6
    },
    {
      "token" : "太棒",
      "start_offset" : 11,
      "end_offset" : 13,
      "type" : "CN_WORD",
      "position" : 7
    },
    {
      "token" : "了",
      "start_offset" : 13,
      "end_offset" : 14,
      "type" : "CN_CHAR",
      "position" : 8
    }
  ]
}
```
## 扩展词词典

随着互联网的发展，“造词运动”也越发的频繁。出现了很多新的词语，在原有的词汇列表中并不存在。比如：“奥力给”，“传智播客” 等。

所以我们的词汇也需要不断的更新，IK分词器提供了扩展词汇的功能。

1）打开IK分词器config目录：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191335394.png)

2）在IKAnalyzer.cfg.xml配置文件内容添加：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典 *** 添加扩展词典-->
        <entry key="ext_dict">ext.dic</entry>
</properties>
```

3）新建一个 ext.dic，可以参考config目录下复制一个配置文件进行修改

```properties
奥力给
```

4）重启elasticsearch

```sh
docker restart es

# 查看 日志
docker logs -f elasticsearch
```

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191336321.png)

日志中已经成功加载ext.dic配置文件

5）测试效果

```json
GET /_analyze
{
  "analyzer": "ik_max_word",
  "text": "Java程序员超过90%,奥力给！"
}
```
## 停用词词典

在互联网项目中，在网络间传输的速度很快，所以很多语言是不允许在网络上传递的，如：关于宗教、政治等敏感词语，那么我们在搜索时也应该忽略当前词汇。

IK分词器也提供了强大的停用词功能，让我们在索引时就直接忽略当前的停用词汇表中的内容。

1）IKAnalyzer.cfg.xml配置文件内容添加：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
<properties>
        <comment>IK Analyzer 扩展配置</comment>
        <!--用户可以在这里配置自己的扩展字典-->
        <entry key="ext_dict">ext.dic</entry>
         <!--用户可以在这里配置自己的扩展停止词字典  *** 添加停用词词典-->
        <entry key="ext_stopwords">stopword.dic</entry>
</properties>
```

3）在 stopword.dic 添加停用词

```dic
习大大
```

4）重启elasticsearch

```sh
# 重启服务
docker restart elasticsearch
docker restart kibana

# 查看 日志
docker logs -f elasticsearch
```

日志中已经成功加载stopword.dic配置文件

5）测试效果：

```json
GET /_analyze
{
  "analyzer": "ik_max_word",
  "text": "习大大都点赞,奥力给！"
}
```

# 部署es集群

在单机上利用docker容器运行多个es实例来模拟es集群。不过生产环境推荐大家每一台服务节点仅部署一个es的实例。

部署es集群可以直接使用docker-compose来完成，但这要求你的Linux虚拟机至少有**4G**的内存空间

## 创建es集群

首先编写一个docker-compose文件，内容如下：

```sh
version: '2.2'
services:
  es01:
    image: elasticsearch:7.12.1
    container_name: es01
    environment:
      - node.name=es01
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es02,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data01:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elastic
  es02:
    image: elasticsearch:7.12.1
    container_name: es02
    environment:
      - node.name=es02
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es03
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data02:/usr/share/elasticsearch/data
    ports:
      - 9201:9200
    networks:
      - elastic
  es03:
    image: elasticsearch:7.12.1
    container_name: es03
    environment:
      - node.name=es03
      - cluster.name=es-docker-cluster
      - discovery.seed_hosts=es01,es02
      - cluster.initial_master_nodes=es01,es02,es03
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    volumes:
      - data03:/usr/share/elasticsearch/data
    networks:
      - elastic
    ports:
      - 9202:9200
volumes:
  data01:
    driver: local
  data02:
    driver: local
  data03:
    driver: local

networks:
  elastic:
    driver: bridge
```

es运行需要修改一些linux系统权限，修改`/etc/sysctl.conf`文件

```sh
vi /etc/sysctl.conf
```

添加下面的内容：

```sh
vm.max_map_count=262144
```

然后执行命令，让配置生效：

```sh
sysctl -p
```

通过docker-compose启动集群：

```sh
docker-compose up -d
```
## 集群状态监控

kibana可以监控es集群，不过新版本需要依赖es的x-pack 功能，配置比较复杂。

这里推荐使用cerebro来监控es集群状态，官方网址：[https://github.com/lmenezes/cerebro](https://github.com/lmenezes/cerebro)

解压即可使用，非常方便。

解压好的目录如下：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191341956.png)

进入对应的bin目录：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191341437.png)

双击其中的cerebro.bat文件即可启动服务。

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191342477.png)

访问[http://localhost:9000](http://localhost:9000) 即可进入管理界面：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191342780.png)

输入你的elasticsearch的任意节点的地址和端口，点击connect即可：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191342137.png)

绿色的条，代表集群处于绿色（健康状态）。

## 创建索引库

### 利用kibana的DevTools创建索引库

在DevTools中输入指令：

```json
PUT /itcast
{
  "settings": {
    "number_of_shards": 3, // 分片数量
    "number_of_replicas": 1 // 副本数量
  },
  "mappings": {
    "properties": {
      // mapping映射定义 ...
    }
  }
}
```

### 利用cerebro创建索引库

利用cerebro还可以创建索引库：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191343284.png)

填写索引库信息：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191343989.png)

## 查看分片效果

回到首页，即可查看索引库分片效果：

![](https://hexo-img-bucket-1306020160.cos.ap-beijing.myqcloud.com/pic/202403191344845.png)