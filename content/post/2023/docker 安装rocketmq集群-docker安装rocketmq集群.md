---
title: docker 安装rocketmq集群
date: 2023-01-10 15:38:08.812
updated: 2024-01-20 23:01:52.306
url: /archives/docker安装rocketmq集群
categories: 
tags: 
---

### 下载rocketmq镜像
```
docker pull   -v   apache/rocketmq:5.1.3
```
###  rocketmq-nameServer

```
docker run -d --name rocketmq-nameServer -p 9876:9876 -p 9878:9878 \
-e JAVA_OPT_EXT="-Xms512m -Xmx512m" \
-v  /opt/rocketmq/nameServer/logs:/home/rocketmq/logs   \
-v /opt/rocketmq/nameServer/conf/namesrv.conf:/home/rocketmq/rocketmq-5.1.4/conf/namesrvr.conf  \
-v /opt/rocketmq/nameServer/DledgerController:/home/rocketmq/DledgerController \
--restart=always apache/rocketmq:5.1.4 sh mqnamesrv   -c /home/rocketmq/rocketmq-5.1.4/conf/namesrvr.conf   
```

### rocketmq-broker

```
docker run -d --name rocketmq-broker   \
-e JAVA_OPT_EXT="-Xms2048m -Xmx2048m" \
 -p 10909:10909 -p 10911:10911 -p 10912:10912 \
-v  /opt/rocketmq/broker/logs:/home/rocketmq/logs   \
-v  /opt/rocketmq/broker/store/:/home/rocketmq/store/  \
-v  /opt/rocketmq/broker/conf/broker.conf:/home/rocketmq/rocketmq-5.1.4/conf/broker.conf  \
-v  /opt/rocketmq/broker/conf/plain_acl.yml:/home/rocketmq/rocketmq-5.1.4/conf/plain_acl.yml  \
--restart=always apache/rocketmq:5.1.4 sh mqbroker  -c /home/rocketmq/rocketmq-5.1.4/conf/broker.conf 
```

### rocketmq-dashboard
需要自己编译源码 开启登录密码
地址 https://github.com/apache/rocketmq-dashboard
```
docker run -d --name rocketmq-dashboard   --ulimit nofile=1024 \
-e JAVA_OPTS="-Xms128m -Xmx128m" \
-v  /opt/rocketmq/rocketmq-dashboard/rocketmq-dashboard-1.0.1-SNAPSHOT.jar:/rocketmq-dashboard.jar \
-v /opt/rocketmq/rocketmq-dashboard/config:/tmp/rocketmq-console/data/ \
-v /opt/rocketmq/rocketmq-dashboard/logs:/root/logs \
-p 8180:8080 --restart=always \
apacherocketmq/rocketmq-dashboard:latest
```
### 权限
默认在conf下的plain_acl.yml
https://rocketmq.apache.org/zh/docs/bestPractice/03access/#1%E6%9D%83%E9%99%90%E6%8E%A7%E5%88%B6%E7%89%B9%E6%80%A7%E4%BB%8B%E7%BB%8D
