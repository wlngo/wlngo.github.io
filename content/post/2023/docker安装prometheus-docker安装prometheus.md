---
title: docker安装prometheus
date: 2023-01-31 09:49:46.939
updated: 2023-08-08 17:25:11.441
url: /archives/docker安装prometheus
categories: 
tags: 
---

```
docker run -d  -p 9090:9090  --name prometheus  \
  -e TZ=Asia/Shanghai \
  -v /etc/timezone:/etc/timezone:ro \
  -v /etc/localtime:/etc/localtime:ro \
 -v /opt/prometheus/config/prometheus.yml:/etc/prometheus/prometheus.yml \
 -v /var/opt/prometheus/data:/prometheus  \
prom/prometheus \
--config.file=/etc/prometheus/prometheus.yml \
--storage.tsdb.path=/prometheus \
--web.console.libraries=/usr/share/prometheus/console_libraries \
--web.enable-admin-api \
--web.enable-lifecycle 
```


- "--config.file=/etc/prometheus/prometheus.yml"
- "--storage.tsdb.path=/prometheus"
- "--storage.tsdb.retention=24h"
- "--web.enable-admin-api"  # 控制对admin HTTP API的访问，其中包括删除时间序列等功能
- "--web.enable-lifecycle"
### node linux 监控
```
docker run -d -p 9100:9100 --name node-exporter --restart=always  --net="host"   --pid="host"  \
 -v "/:/host:ro,rslave"  \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
 quay.io/prometheus/node-exporter:latest  \
 --path.rootfs=/host
```

### mysql  监控
```
docker run -d    --name mysqld-exporter  --restart=always \
-p 9104:9104 \
 -v /opt/mysqld-exporter/config/:/opt/mysqld-exporter/config/  \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
prom/mysqld-exporter:main \
--mysqld.address=192.168.123.124:3306 \
--config.my-cnf=/opt/mysqld-exporter/config/.my.cnf 
```