---
title: nexus3 批量导入jar包
date: 2022-07-13 17:45:35.139
updated: 2022-07-13 17:59:30.59
url: /archives/nexus3-pi-liang-dao-ru-jar-bao
categories: 
tags: 
---

设置nexus3 混合仓库
![image-1657706298018](/upload/2022/07/image-1657706298018.png)

允许重新部署和更新
![image-1657706333010](/upload/2022/07/image-1657706333010.png)
批量导入shell脚本
```
#!/bin/bash
 
# copy and run this script to the root of the repository directory containing files
# this script attempts to exclude uploading itself explicitly so the script name is important
# Get command line params
while getopts ":r:u:p:" opt; do
	case $opt in
		r) REPO_URL="$OPTARG"
		;;
		u) USERNAME="$OPTARG"
		;;
		p) PASSWORD="$OPTARG"
		;;
	esac
done
 
find . -type f -not -path './mavenimport\.sh*' -not -path '*/\.*' -not -path '*/\^archetype\-catalog\.xml*' -not -path '*/\^maven\-metadata\-local*\.xml' -not -path '*/\^maven\-metadata\-deployment*\.xml' | sed "s|^\./||" | xargs -I '{}' curl -u "$USERNAME:$PASSWORD" -X PUT -v -T {} ${REPO_URL}/{} ;
```

可以命名为mavenimport.sh 

使用方法
./mavenimport.sh  -u 你的nexus3账号 -p 你的nexus3密码 -r  仓库地址