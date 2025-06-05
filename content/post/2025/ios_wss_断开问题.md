---
title: ios wss 断开问题
date: 2025-02-26 10:46:28.54
updated: 2025-02-26 11:02:13.916
url: /archives/ios-wss-question
categories:
tags:
---
当然可以！以下是整理成Markdown格式的总结文档，详细说明如何处理ISO 15 WSS断开问题，并关闭`permessage-deflate`扩展。

---

# 处理IOS WSS断开问题并关闭 `permessage-deflate`

## 背景

在IOS使用WebSocket（WSS）进行通信时，有时会出现连接断开的问题。可能需要关闭`permessage-deflate`扩展以避免兼容性问题。本文档将详细介绍如何在Nginx和Spring Boot应用中关闭`permessage-deflate`扩展，以解决WSS断开问题。


## 解决方案

### 1. 关闭 `permessage-deflate` 扩展

`permessage-deflate` 是一种WebSocket压缩扩展，用于减少传输的数据量。但在某些情况下，它可能导致兼容性问题，特别是在特定的标准（如ISO 15）中。因此，我们需要关闭此扩展。

#### 1.1 在 Nginx 中关闭 `permessage-deflate`

在Nginx配置文件中，可以通过设置`proxy_set_header`来控制WebSocket扩展。具体步骤如下：

1. **编辑Nginx配置文件**：
   通常位于 `/etc/nginx/nginx.conf` 或 `/etc/nginx/conf.d/default.conf`。

2. **添加或修改以下配置**：
   在 `location` 块中添加或修改 `proxy_set_header` 指令，以禁用 `permessage-deflate`。

   ```nginx
   server {
       listen 443 ssl;
       server_name example.com;

       ssl_certificate /path/to/certificate.pem;
       ssl_certificate_key /path/to/privatekey.pem;

       location /ws {
           proxy_pass http://backend_server;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection "upgrade";
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
           #重点
           proxy_set_header Sec-WebSocket-Extensions 'client_max_window_bits=0';
       }
   }
   ```

   注意：
    - `Sec-WebSocket-Extensions 'client_max_window_bits=0'` 表示禁用 `permessage-deflate`。
    - 如果需要完全禁用所有扩展，可以设置为空字符串：`Sec-WebSocket-Extensions ''`。

3. **重新加载Nginx配置**：
   ```sh
   sudo nginx -s reload
   ```

#### 1.2 在 Spring Boot 中关闭 `permessage-deflate`

在Spring Boot应用中，可以通过tomcat系统属性来禁用内置的WebSocket扩展。

1. 令行参数设置，可以在启动Spring Boot应用时添加以下参数：

   ```sh
   java -Dorg.apache.tomcat.websocket.DISABLE_BUILTIN_EXTENSIONS=true -jar your-application.jar
   ```
```