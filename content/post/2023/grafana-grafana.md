---
title: grafana
date: 2023-04-16 21:40:08.073
updated: 2023-08-08 17:26:51.779
url: /archives/grafana
categories: 
tags: 
---

```
docker run -d --name=grafana -p 3000:3000  \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
-v /var/opt/grafana/data/:/var/lib/grafana \
grafana/grafana
```