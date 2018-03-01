---
title: "Remove docker info to get more space"
date: 2018-03-01T11:05:46+08:00
tags: ["docker","docker log","docker image","docker volume"]
draft: false
---

------------------

# Docker容器清理

## 日志清理

### 位置

* docker日志是放在/var/lib/docker/containers/containerId/*-json.log

### 清除

* 清除方式 cat /dev/null > $log

```bash
echo "==================== start clean docker containers logs =========================="  
  
logs=$(find /var/lib/docker/containers/ -name *-json.log)  
  
for log in $logs  
        do  
                echo "clean logs : $log"  
                cat /dev/null > $log  
        done  
  
  
echo "==================== end clean docker containers logs   =========================="  
```



## 停止的容器清除

```bash
docker rm $(docker ps -a  | grep 'Exited' | awk '{print $1}')
docker rm $(docker ps -a  | grep 'Dead' | awk '{print $1}')
```

## 删除无用的volume

```bash
docker volume rm $(docker volume ls -f dangling=true | awk '{print $2}')
```

## 清除不用的images

* 根据image的使用情况自行清理