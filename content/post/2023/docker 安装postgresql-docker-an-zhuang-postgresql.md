---
title: docker 安装postgresql
date: 2023-03-28 20:21:30.09
updated: 2023-04-16 18:30:20.85
url: /archives/docker-an-zhuang-postgresql
categories: 
tags: 
---

```
 docker run -d   --name postgres --restart=always --network elknetwork \
    -p 5432:5432  -e POSTGRES_PASSWORD_FILE=/opt/postgres/config/postgres-password \
    -v /var/opt/postgres/data:/var/lib/postgresql/data \
    -v /opt/postgres/config/:/opt/postgres/config/  \
    -v /etc/timezone:/etc/timezone:ro -v /etc/localtime:/etc/localtime:ro \
  	weiliangning.work:18040/docker/postgres:15.2
```