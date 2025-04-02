---
title: chrome解决http自动跳转https问题
date: 2022-12-16 15:44:51.649
updated: 2022-12-16 15:44:51.649
url: /archives/chrome-jie-jue-http-zi-dong-tiao-zhuan-https-wen-ti
categories: 
tags: 
---

## 打开管理页面
打开chrome浏览器，地址栏输入以下协议并回车

```
chrome://net-internals/#hsts
```
浏览器中将会出现以下页面，可以通过以下操作将已经自动转https协议的域名从chrome中移除

1. Domain Security Prolicy
2. Delete remain security prolicies

