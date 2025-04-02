---
title: docker 安装  mongodb
date: 2022-08-01 18:11:44.253
updated: 2022-08-02 10:34:21.515
url: /archives/docker-an-zhuang-mongodb
categories: 
tags: 
---

#### 拉取镜像
```
docker pull mongo
```
```
docker pull mongo
```
#### 运行容器

```
 docker run --name some-mongo -d --net mongo -p 27017:27017 --restart=on-fa
ilure:3 mongo
```
###### 可视化web
```
docker run -d --name  mongo-express --net mongo -e ME_CONFIG_MONGODB_SERVER=some-mongo -p 8081:8081 --restart=always mongo-express
```