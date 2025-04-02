---
title: 安装  sonarqube
date: 2023-04-16 18:07:17.297
updated: 2023-08-27 00:05:04.775
url: /archives/安装sonarqube
categories: 
tags: 
---

```
docker run -d --name sonarqube \
    -p 9000:9000 \
    -e SONAR_JDBC_URL=jdbc:postgresql://www.weiliangning.work:5432/ \
    -e SONAR_JDBC_USERNAME=postgres \
    -e SONAR_JDBC_PASSWORD=... \
    -v /opt/sonarqube/data:/opt/sonarqube/data \
    -v /opt/sonarqube/extensions:/opt/sonarqube/extensions \
    -v /opt/sonarqube/logs:/opt/sonarqube/logs \
    sonarqube:lts
```