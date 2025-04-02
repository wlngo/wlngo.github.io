---
title: docker 安装mongo 
date: 2023-04-16 17:14:58.455
updated: 2023-08-08 10:12:26.221
url: /archives/docker安装mongo
categories: 
tags: 
---

https://hub.docker.com/_/mongo
```
docker run --name mongo  -d   --restart=always \
-p 27017:27017  \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
-v /var/opt/mongo/data:/data/db \
-e MONGO_INITDB_ROOT_USERNAME=root \
-e MONGO_INITDB_ROOT_PASSWORD=secret \
mongo:6.0-rc
```