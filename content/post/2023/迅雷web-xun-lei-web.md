---
title: 迅雷web
date: 2023-11-09 08:56:08.87
updated: 2023-11-09 08:56:08.87
url: /archives/xun-lei-web
categories: 
tags: 
---

```
docker run -d --name=xunlei  -p 2345:2345 -v /opt/xunlei/data:/xunlei/data -v /opt/qbittorrent/downloads:/xunlei/downloads --restart=always --privileged cnk3x/xunlei:latest
```