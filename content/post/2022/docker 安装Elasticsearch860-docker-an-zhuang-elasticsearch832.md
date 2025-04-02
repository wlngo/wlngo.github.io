---
title: docker 安装Elasticsearch:8.6.0
date: 2022-07-28 20:17:55.962
updated: 2023-08-08 11:08:34.216
url: /archives/docker-an-zhuang-elasticsearch832
categories: 
- Dcoker
tags: 
---

#### 下载镜像
```
docker pull elasticsearch:8.6.0
```

#### 创建docke网络
```
docker  network create elknetwork
```

#### 运行镜像 
```
docker run -d --name elasticsearch --restart=always   \
-p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"  \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
-v /opt/elk/elasticsearch/config:/usr/share/elasticsearch/config \
-v /opt/elk/elasticsearch/data:/usr/share/elasticsearch/data \
-v /opt/elk/elasticsearch/plugins:/usr/share/elasticsearch/plugins \
-v /opt/elk/elasticsearch/logs:/usr/share/elasticsearch/logs \
 elasticsearch:8.6.0
```