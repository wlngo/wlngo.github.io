---
title: chatgpt-next-web 安装
date: 2024-05-17 17:53:15.869
updated: 2024-05-17 18:52:49.991
url: /archives/chatgpt-next-web安装
categories: 
tags: 
---

[githu地址](https://github.com/ChatGPTNextWeb/ChatGPT-Next-Web)


```
docker run -d --name chatgpt-next-web -p 3000:3000  --restart=always \
-e OPENAI_API_KEY="sk-"  \
-e BASE_URL="https://openkey.cloud" \
-e CODE=2411228533  \
yidadaa/chatgpt-next-web
```