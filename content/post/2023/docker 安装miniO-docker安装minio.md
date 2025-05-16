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

```
docker run -d -p 9000:9000 -p 9050:9050 \
--name minio --restart=always \
-e "MINIO_ROOT_USER=账号" \
-e "MINIO_ROOT_PASSWORD=密码!" \
-e  "MINIO_DOMAIN"=wlngo.top \
-v /opt/minio/data:/data \
-v /opt/minio/config:/root/.minio \
-e TZ=Asia/Shanghai \
  quay.io/minio/minio server  \
/data --console-address ":9050"


```

nginx
```
upstream minio_s3 {
   least_conn;
   server 192.168.123.124:9000;
}

upstream minio_console {
   least_conn;
   server 192.168.123.124:9050;
}
server {
   listen       9000 ssl;
   listen  [::]:9000 ssl;

   # Allow special characters in headers
   ignore_invalid_headers off;
   # Allow any size file to be uploaded.
   # Set to a value such as 1000m; to restrict file size to a specific value
   client_max_body_size 0;
   # Disable buffering
   proxy_buffering off;
   proxy_request_buffering off;
   #请填写证书文件的相对路径或绝对路径
   ssl_certificate /etc/nginx/ssl/wlngo.top_nginx/wlngo.top_bundle.crt;
   #请填写私钥文件的相对路径或绝对路径
   ssl_certificate_key /etc/nginx/ssl/wlngo.top_nginx/wlngo.top.key;
   ssl_session_timeout 5m;
   #请按照以下协议配置
   ssl_protocols TLSv1.2 TLSv1.3;
   #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
   ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
   ssl_prefer_server_ciphers on;

   location / {
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;

      proxy_connect_timeout 300;
      # Default is HTTP/1, keepalive is only enabled in HTTP/1.1
      proxy_http_version 1.1;
      proxy_set_header Connection "";
      chunked_transfer_encoding off;

      proxy_pass http://minio_s3; # This uses the upstream directive definition to load balance
   }
}

server {

   listen       9050 ssl;
   listen  [::]:9050 ssl;

   # Allow special characters in headers
   ignore_invalid_headers off;
   # Allow any size file to be uploaded.
   # Set to a value such as 1000m; to restrict file size to a specific value
   client_max_body_size 0;
   # Disable buffering
   proxy_buffering off;
   proxy_request_buffering off;
   #请填写证书文件的相对路径或绝对路径
   ssl_certificate /etc/nginx/ssl/wlngo.top_nginx/wlngo.top_bundle.crt;
   #请填写私钥文件的相对路径或绝对路径
   ssl_certificate_key /etc/nginx/ssl/wlngo.top_nginx/wlngo.top.key;
   ssl_session_timeout 5m;
   #请按照以下协议配置
   ssl_protocols TLSv1.2 TLSv1.3;
   #请按照以下套件配置，配置加密套件，写法遵循 openssl 标准。
   ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
   ssl_prefer_server_ciphers on;

   location / {
      proxy_set_header Host $http_host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-NginX-Proxy true;

      # This is necessary to pass the correct IP to be hashed
      real_ip_header X-Real-IP;

      proxy_connect_timeout 300;

      # To support websocket
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";

      chunked_transfer_encoding off;

      proxy_pass http://minio_console/; # This uses the upstream directive definition to load balance
   }
}


```
https
1.证书准备
将申请的SSL证书 .key结尾的重命名为：private.key和 .crt结尾的重命名为：public.crt 别问为什么，官方规定，不信自己看官方文档：https://min.io/docs/minio/linux/operations/network-encryption.html?ref=docs-redirect
 然后将private.key和public.crt两个文件放到MinIO目录certs下面：/你的minio目录/config/certs
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