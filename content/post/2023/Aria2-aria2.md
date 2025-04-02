---
title: Aria2
date: 2023-04-28 17:24:56.601
updated: 2023-04-30 18:58:02.382
url: /archives/aria2
categories: 
tags: 
---

[docker hub镜像](https://hub.docker.com/r/p3terx/aria2-pro)
```
docker pull p3terx/aria2-pro
```
启动命令
```
docker run -d \
    --name aria2-pro \
    --restart always \
    --log-opt max-size=1m \
    -e PUID=$UID \
    -e PGID=$GID \
    -e UMASK_SET=022 \
 	-e RPC_SECRET=2411228533 \
    -e RPC_PORT=6800 \
    -e LISTEN_PORT=6888 \
    --network=elknetwork \
    -v /opt/aria2/server/aria2-config:/config \
    -v /opt/aria2/server/aria2-downloads:/downloads \
    p3terx/aria2-pro
```