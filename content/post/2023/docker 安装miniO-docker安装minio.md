---
title: docker 安装miniO
date: 2023-02-16 17:01:22.928
updated: 2024-03-12 15:32:46.849
url: /archives/docker安装minio
categories: 
tags: 
---

## 启动
```
docker run -d -p 9000:9000 -p 9050:9050 \
--name minio --restart=always \
-e "MINIO_SERVER_URL=https://wlngo.top:9000" \
-e "MINIO_ROOT_USER=账号" \
-e "MINIO_ROOT_PASSWORD=密码" \
-e "MINIO_BROWSER_REDIRECT_URL=https://wlngo.top:9050" \
-e  "MINIO_DOMAIN"=wlngo.top \
-v /var/opt/minio/data:/data \
-v /opt/minio/config:/root/.minio \
-e TZ=Asia/Shanghai \
-v /etc/timezone:/etc/timezone:ro \
-v /etc/localtime:/etc/localtime:ro \
  quay.io/minio/minio server  \
/data --console-address ":9050"
```
https
1.证书准备
将申请的SSL证书 .key结尾的重命名为：private.key和 .crt结尾的重命名为：public.crt 别问为什么，官方规定，不信自己看官方文档：https://min.io/docs/minio/linux/operations/network-encryption.html?ref=docs-redirect
 然后将private.key和public.crt两个文件放到MinIO目录certs下面：/你的minio目录/config/certs
 ![image](/upload/2023/02/image.png)
##  监控
```
 docker run -it --entrypoint=/bin/sh minio/mc
```
```
mc alias set myminio https://www.weiliangning.work:9000  账号 密码
```
```
mc admin prometheus generate myminio
```