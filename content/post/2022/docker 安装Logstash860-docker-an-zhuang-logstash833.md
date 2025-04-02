---
title: docker 安装Logstash:8.6.0
date: 2022-07-30 11:05:13.463
updated: 2023-04-14 17:22:36.742
url: /archives/docker-an-zhuang-logstash833
categories: 
- Dcoker
tags: 
---

#### 拉去镜像
```
docker pull logstash:8.6.0
```

#### 运行logstash
```
docker run -d  -it --name logstash  --net elknetwork \
-p 5044:5044  -p 9600:9600 --restart=always \
-v /opt/javarun/:/opt/javarun \
-v /opt/elk/logstash/data:/usr/share/logstash/data \
-v /opt/elk/logstash/pipeline/:/usr/share/logstash/pipeline/ \
-v /opt/elk/logstash/config/:/usr/share/logstash/config/ \
logstash:8.6.0
```