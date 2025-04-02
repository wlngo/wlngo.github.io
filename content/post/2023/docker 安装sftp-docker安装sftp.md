---
title: docker 安装sftp
date: 2023-09-01 14:59:32.338
updated: 2023-09-01 14:59:34.117
url: /archives/docker安装sftp
categories: 
tags: 
---

命令
```
docker run \
   --restart=always \
    -e LANG=en_US.UTF-8 \
    -e LANGUAGE=en_US:en \
    -e LC_ALL=en_US.UTF-8 \
    -e TZ=Asia/Shanghai \
  --name=sftp  \
    -v /opt/qbittorrent/downloads:/home/foo/upload \
    -p 44:22 -d atmoz/sftp foo:pass:::upload
```