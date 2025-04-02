---
title:  zookeeper docker  
date: 2023-04-14 21:11:53.329
updated: 2024-01-20 22:38:34.755
url: /archives/zookeeperdocker
categories: 
tags: 
---

## 集群需要修改 data 下 myid 分别为1 2 3  (此处是伪集群模式)
## zookeeper-a

```
docker run --name zookeeper-a --restart always -d \
-v /opt/zookeeper/zookeeper-a/conf/zoo.cfg:/conf/zoo.cfg  \
-v /opt/zookeeper/zookeeper-a/datalog/:/datalog  \
-v /opt/zookeeper/zookeeper-a/data/:/data \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
--network=host \
zookeeper
```
## zookeeper-b
```
docker run --name zookeeper-b --restart always -d   \
-v /opt/zookeeper/zookeeper-b/conf/zoo.cfg:/conf/zoo.cfg  \
-v /opt/zookeeper/zookeeper-b/datalog/:/datalog  \
-v /opt/zookeeper/zookeeper-b/data/:/data \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
--network=host \
zookeeper
```
## zookeeper-c

```
docker run --name zookeeper-c --restart always -d   \
-v /opt/zookeeper/zookeeper-c/conf/zoo.cfg:/conf/zoo.cfg  \
-v /opt/zookeeper/zookeeper-c/datalog/:/datalog  \
-v /opt/zookeeper/zookeeper-c/data/:/data \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
--network=host \
zookeeper
```

## zookeeper集群

```
docker run --name zookeeper --restart always -d   \
-e TZ=Asia/Shanghai \
-v /opt/zookeeper/conf/zoo.cfg:/conf/zoo.cfg  \
-v /opt/zookeeper/datalog/:/datalog  \
-v /opt/zookeeper/data/:/data \
--network=host \
zookeeper:3.9.1-jre-17
```

