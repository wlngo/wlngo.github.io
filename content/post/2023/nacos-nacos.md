---
title: nacos
date: 2023-10-23 17:43:31.378
updated: 2024-01-05 11:23:51.38
url: /archives/nacos
categories: 
tags: 
---

docker 部署集群
```
docker run --name nacos --restart always \
-e TZ=Asia/Shanghai \
-e JVM_XMS=512m \
-e JVM_XMX=512m \
-e JVM_XMN=256m \
-e JVM_XMS=64m \
-e JVM_MMS=160m \
-e NACOS_SERVER_IP=192.168.123.242 \
-e NACOS_SERVERS="192.168.123.221:8848 192.168.123.176:8848  192.168.123.242:8848" \
-e SPRING_DATASOURCE_PLATFORM=mysql \
-e MYSQL_SERVICE_HOST=10.0.0.3 \
-e  MYSQL_SERVICE_PORT=4000 \
-e MYSQL_SERVICE_DB_NAME=nacos \
-e MYSQL_SERVICE_USER="root" \
-e MYSQL_SERVICE_PASSWORD="密码" \
-e NACOS_AUTH_IDENTITY_KEY="nacos" \
-e NACOS_AUTH_IDENTITY_VALUE="密码" \
-e NACOS_AUTH_ENABLE="true" \
-e NACOS_AUTH_TOKEN="CbUgzg/ZXr9j3nDJ/IZvznPk5A5PtkdgHM3m7odHII4=" \
-p 8848:8848 -p 9848:9848 -p 9849:9849 -p7848:7848 -d \
 nacos/nacos-server:v2.3.0
```
nacos-syc
```
docker run --name nacos-sync --restart always   -d  \
-e TZ=Asia/Shanghai \
-p 8083:8083 \
weiliangning.work:18040/docker/nacos-syc
```