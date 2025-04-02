---
title: qbittorrent 安装
date: 2023-04-30 18:57:39.393
updated: 2023-05-01 02:42:59.397
url: /archives/qbittorrent-an-zhuang
categories: 
tags: 
---

docker run -d --name=qbittorrent -e PUID=1000 -e PGID=1000 \
-e TZ=Asia/Shangha -e WEBUI_PORT=8060 -p 8060:8060  -p 16881:16881 \
-v /opt/qbittorrent/config:/config -v /opt/qbittorrent/downloads:/downloads --restart always \
linuxserver/qbittorrent