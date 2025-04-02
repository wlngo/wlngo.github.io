---
title: filebrowser
date: 2023-04-30 19:34:22.488
updated: 2023-04-30 19:34:24.669
url: /archives/filebrowser
categories: 
tags: 
---

```
docker run \
    -d --name filebrowser \
    -v /opt/qbittorrent/downloads/:/srv \
    -v /opt/filebrowser/config/filebrowser.db:/database/filebrowser.db \
    -v /opt/filebrowser/config/settings.json:/config/settings.json \
    -p 8070:80 \
    filebrowser/filebrowser:s6
```