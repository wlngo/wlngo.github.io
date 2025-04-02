---
title: JFROG java 二进制包管理工具 
date: 2022-08-03 10:29:54.757
updated: 2023-08-15 20:44:06.331
url: /archives/jfrogjava-er-jin-zhi-bao-guan-li-gong-ju
categories: 
tags: 
---

#### 官方文档
https://www.jfrog.com/confluence/display/JFROG/Installing%20Artifactory
安装
```
mkdir -p $JFROG_HOME/artifactory-jcr
mkdir -p $JFROG_HOME/artifactory-oss
mkdir -p $JFROG_HOME/artifactory-oss/var/etc/
cd $JFROG_HOME/artifactory-oss/var/etc/
touch ./system.yaml
chown -R 1030:1030 $JFROG_HOME/artifactory-oss/var
mkdir -p $JFROG_HOME/artifactory-jcr/var/etc/
cd $JFROG_HOME/artifactory-jcr/var/etc/
touch ./system.yaml
chown -R 1030:1030 $JFROG_HOME/artifactory-jcr/var
docker run --restart=always --name artifactory-oss -v $JFROG_HOME/artifactory-oss/var/:/var/opt/jfrog/artifactory -d -p 8010:8081 -p 8020:8082 releases-docker.jfrog.io/jfrog/artifactory-oss:7.49.10
docker run --restart=always --name artifactory-jcr -v $JFROG_HOME/artifactory-jcr/var/:/var/opt/jfrog/artifactory -d -p 8030:8081 -p 8040:8082 releases-docker.jfrog.io/jfrog/artifactory-jcr:7.49.10
```
nginx 代理端口要保持一致
配置需要从管理页面下载


### 限制内存
system.yaml
```
shared:
    extraJavaOpts: "-server -Xms512m -Xmx2g -Xss256k -XX:+UseG1GC"
```