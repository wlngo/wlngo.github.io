---
title: showdoc docker 安装
date: 2023-04-14 16:06:25.641
updated: 2023-04-16 18:28:14.395
url: /archives/showdocdocker-an-zhuang
categories: 
tags: 
---

```
docker run -d --name showdoc --user=root --privileged=true --restart always -p 4999:80 \
-v /var/opt/showdoc/data:/var/www/html/  \
star7th/showdoc
```
