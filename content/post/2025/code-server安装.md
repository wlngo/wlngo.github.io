---
title: code-server安装
date: 2025-06-26
updated: 2025-06-26 
url: /archives/code-server
categories:
tags:
---

# ☕ code-server安装

详细文档见：[https://coder.com/docs/code-server/install#fedora-centos-rhel-suse](https://coder.com/docs/code-server/install#fedora-centos-rhel-suse)

## 子域名配置

文档见：[https://coder.com/docs/code-server/guide#using-a-subdomain](https://coder.com/docs/code-server/guide#using-a-subdomain)

### systemd 服务配置示例

```ini
[Unit]
Description=code-server
After=network.target

[Service]
Type=exec
ExecStart=/usr/bin/code-server --proxy-domain api.wlngo.top
Restart=always
User=%i

[Install]
WantedBy=default.target
```

# 使用 acme.sh 申请 SSL 证书（DNS 验证 - 腾讯云 DNS）



## 申请证书

使用 `dns_dp` 插件（腾讯云 DNS）通过 DNS 验证申请 `wlngo.top` 及其通配符证书：

```bash

export DP_Id="你的腾讯云 SecretId"
export DP_Key="你的腾讯云 SecretKey"
./acme.sh --issue -d wlngo.top -d '*.wlngo.top' -d '*api.wlngo.top' --dns dns_dp

```
## 自动续期任务

```bash
./acme.sh --install-cert -d wlngo.top   --key-file /etc/nginx/ssl/wlngo.top.key   --fullchain-file /etc/nginx/ssl/wlngo.top.fullchain.pem   --reloadcmd "systemctl reload nginx"
```


# Nginx 配置示例

```
server {
    listen 80;
    # 排除 80 和 443 端口（通过正则）
    server_name ~^(?!(80|443))(?<port>\d+)\.api\.wlngo\.top$;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name ~^(?!(80|443))(?<port>\d+)\.api\.wlngo\.top$;

    ssl_certificate     /etc/nginx/ssl/wlngo.top.fullchain.pem;
    ssl_certificate_key /etc/nginx/ssl/wlngo.top.key;

    location / {
        proxy_pass http://127.0.0.1:$port;
        proxy_set_header Host $host;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection upgrade;
    }
}
```