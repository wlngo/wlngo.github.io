---
title: apisix  安装
date: 2023-09-25 17:06:16.494
updated: 2023-10-27 11:11:48.206
url: /archives/apisix安装
categories: 
tags: 
---

etcd 安装

```
docker run -d  --restart always  \
--name etcd -p 2379:2379 -p 2380:2380 \
-e ALLOW_NONE_AUTHENTICATION=yes \
-e ETCD_ADVERTISE_CLIENT_URLS=http://192.168.123.185:2379 \
-e TZ=Asia/Shanghai \
-v /opt/etcd/data:/bitnami/etcd/data \
 bitnami/etcd:3.5
```
apisix 安装

```
 docker run -d --name apisix --restart always \
 -e TZ=Asia/Shanghai \
 -p 9080:9080 -p 9443:9443  -p 9180:9180 -p 9091:9091 \
 -v /opt/apisix/conf/config.yaml:/usr/local/apisix/conf/config.yaml \
 apache/apisix:3.2.2-debian
```
 dashboard 安装
```
docker run -d --name apisix-dashboard --restart always \
-e TZ=Asia/Shanghai \
-p 9000:9000 \
-v /opt/apisix-dashboard/config/conf.yaml:/usr/local/apisix-dashboard/conf/conf.yaml \
-v /opt/apisix-dashboard/logs:/usr/local/apisix-dashboard/logs/ \
apache/apisix-dashboard
```