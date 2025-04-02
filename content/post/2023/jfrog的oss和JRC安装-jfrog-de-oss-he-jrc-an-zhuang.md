
---
title: jfrog的oss和JRC安装
date: 2023-03-14 10:01:17.05
updated: 2023-07-31 10:47:25.602
url: /archives/jfrog-de-oss-he-jrc-an-zhuang
categories: 
tags: 
---

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