---
title: zookeeper 权限配置
date: 2023-02-27 11:24:41.165
updated: 2023-02-27 11:24:41.165
url: /archives/zookeeper-quan-xian-pei-zhi
categories: 
tags: 
---

查看当前身份认证
```
getAcl /
```

Digest 是最常用的权限控制模式，也更符合我们对于权限控制的认识，其类似于 username:password 形式的权限标识来进行权限配置，便于区分不同应用来进行权限控制。

当我们通过 username:password 形式配置了权限标识。这里的密码是密文，ZooKeeper 会对其先后进行两次编码处理，分别是SHA-1算法加密和BASE64编码，在 SHELL 中可以通过以下命令计算：
```
echo -n<user>:<password> | openssl dgst -binary -sha1 | openssl base64
```
例子：
```
setAcl /test/digest-node-1 digest:user-1:1g4T1B5w+se9ntA6Ckp90uPaJ30=:crdwa
```