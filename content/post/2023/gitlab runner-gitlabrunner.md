---
title: gitlab runner
date: 2023-03-29 15:30:49.726
updated: 2024-03-20 15:14:26.317
url: /archives/gitlabrunner
categories: 
tags: 
---

```

docker run -d --name gitlab-runner --restart always \
  -v /opt/gitlab-runner/config:/etc/gitlab-runner  \
  -v /var/run/docker.sock:/var/run/docker.sock  \
   gitlab/gitlab-runner:latest
```

```
docker exec -it gitlab-runner /bin/bash
```
