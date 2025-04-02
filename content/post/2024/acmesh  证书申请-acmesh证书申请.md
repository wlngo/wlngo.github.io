---
title: acme.sh  证书申请
date: 2024-03-12 14:28:33.289
updated: 2024-03-12 15:24:05.264
url: /archives/acmesh证书申请
categories: 
tags: 
---

安装
```
curl https://get.acme.sh | sh -s email=my@example.com
```
卸载
```
acme.sh --uninstall
```
设置提供商
```
acme.sh --set-default-ca --server letsencrypt
```
```
acme.sh --set-default-ca --server zerossl
```

DNSPod.cn 域名 API 选项要求您首先登录您的帐户以获取 DNSPod API 密钥和 ID。

```
export DP_Id="<id>"
export DP_Key="<key>"
```

申请
```
./acme.sh --insecure --dnssleep 600 --issue --dns dns_dp -d wlngo.top -d *.wlngo.top
```
删除
```
acme.sh  --remove  -d wlngo.top
```
部署
```
./acme.sh --install-cert -d  wlngo.top  \
--key-file      /etc/nginx/ssl/wlngo.top.key \
--fullchain-file /etc/nginx/ssl/wlngo.top_bundle.crt  \
--reloadcmd      "sh /root/.acme.sh/reload.sh" 
```
reload.sh 脚本
```
#!/usr/bin/bash

systemctl force-reload nginx
cp  /etc/nginx/ssl/wlngo.top.key   /opt/gitlab/config/ssl/wlngo.top.key
cp  /etc/nginx/ssl/wlngo.top_bundle.crt /opt/gitlab/config/ssl/
docker exec -it gitlab /bin/bash gitlab-ctl reconfigure
```
                                                          
自动更新
```
acme.sh --upgrade --auto-upgrade
```
自动更新 关闭
```
acme.sh --upgrade --auto-upgrade 0
```
