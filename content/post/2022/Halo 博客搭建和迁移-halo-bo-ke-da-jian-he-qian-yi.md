---
title: Halo 博客搭建和迁移
date: 2022-07-30 10:53:08.301
updated: 2022-07-30 10:53:08.301
url: /archives/halo-bo-ke-da-jian-he-qian-yi
categories: 
tags: 
---

## Halo 博客官网文档

https://docs.halo.run/

# docker 运行命令
```
docker run -it -d --name halo -p 8090:8090 \
-v ~/.halo2:/root/.halo2 -e JVM_OPTS="-Xmx1024m -Xms1024m" \
 registry.fit2cloud.com/halo/halo:2.20 \
 --spring.r2dbc.url=r2dbc:pool:mysql://192.168.123.124:3306/halo2 \
 --spring.r2dbc.username=root \
 --spring.r2dbc.password=password \
 --spring.sql.init.platform=mysql
```