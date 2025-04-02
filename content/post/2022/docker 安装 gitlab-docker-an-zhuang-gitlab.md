---
title: docker 安装 gitlab
date: 2022-07-30 11:23:46.585
updated: 2024-06-21 18:11:03.457
url: /archives/docker-an-zhuang-gitlab
categories: 
tags: 
---

#### 官网文档
https://docs.gitlab.cn/jh/install/docker.html

命令(最新版本16.2.1)
```
sudo docker run --detach \
  --hostname www.wlngo.top \
  --publish 8443:8443 --publish 8480:80 --publish 8422:22 --publish 8450:8450\
  --name gitlab \
  --restart always \
  --volume $GITLAB_HOME/config:/etc/gitlab \
  --volume $GITLAB_HOME/logs:/var/log/gitlab \
  --volume $GITLAB_HOME/data:/var/opt/gitlab \
  --shm-size 256m \
  gitlab/gitlab-ee:17.0.2-ee.0
```
```
sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

https://docs.gitlab.cn/omnibus/settings/smtp.html#qq-exmail

```
#邮箱发件人名字
gitlab_rails['gitlab_email_display_name'] = '魏亮宁'
```
```
docker exec -it gitlab /bin/bash
gitlab-ctl reconfigure
gitlab-rails console
Notify.test_email('2252603132@qq.com','test','test').deliver_now
```