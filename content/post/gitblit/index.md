---
title: gitblit 安装
description: Docker 安装git 私服 gitblit
slug: gitblit
date: 2022-07-09 22:12:00+0800
categories:
    - git
tags:
    - docker
---
# 安装命令

拉取镜像
```
docker pull jacekkow/gitblit
```

启动镜像
```
sudo docker run -d --name gitblit 
 -v /opt/gitblit-data:/opt/gitblit-data
 -p 8443:8443 -p 8080:8080 -p 9418:9418 -p 29418:29418 
 --restart=always  jacekkow/gitblit

```
