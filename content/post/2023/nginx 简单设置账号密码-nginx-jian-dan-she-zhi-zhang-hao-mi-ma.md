---
title: nginx 简单设置账号密码
date: 2023-03-17 17:17:38.837
updated: 2023-03-17 17:17:38.837
url: /archives/nginx-jian-dan-she-zhi-zhang-hao-mi-ma
categories: 
tags: 
---

nginx 设置
```
auth_basic "请输入用户名和密码";
auth_basic_user_file /etc/nginx/auth/htpasswd;
```
sudo sh -c "echo -n '账号:' >> htpasswd"
sudo sh -c "openssl passwd -apr1 >> htpasswd"
请输入密码