---
title: nexus3 批量导入npm包
date: 2022-07-14 19:07:21.188
updated: 2022-07-14 19:07:21.188
url: /archives/nexus3-pi-liang-dao-ru-npm-bao
categories: 
tags: 
---

```
#!/bin/bash
REPO_URL='http://10.33.6.49:8081/repository/yapi_npm/'
USERNAME='admin'
PASSWORD='admin123'
MULU='/home/keyvalue/Documents/node_modules'
HASFILE='package.json'
 
for file in "$MULU"/*; 
do 
if [ -d "$file" ]; 
then
	# echo $file 
    #这里写上传代码 $file是目录名
    if [ -f "$file/$HASFILE" ];then
	    echo "$file/$HASFILE"
	    cd "$file"
	    npm publish --registry "$REPO_URL"
	fi;
  
fi; 
done
```