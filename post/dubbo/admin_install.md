---
title: "admin install"
date: 2018-03-02T15:07:46+08:00
tags: ["dubbo"]
draft: false
---

------



# 管理控制台安装

## 下载源码

```bash
wget https://github.com/alibaba/dubbo/archive/dubbo-2.6.0.zip
unzip dubbo-2.6.0.zip
cd dubbo-dubbo-2.6.0/
```

## 编译源码

```bash
docker run -it --rm --name my-maven-project -v "$PWD":/usr/src/mymaven -w /usr/src/mymaven maven:3.2-jdk-8 mvn clean install
```

## 拷贝运行文件并解压

```bash
mkdir -p /vagrant/dubbo/admin
cd /vagrant/dubbo/
cp /vagrant/dubbo/monitor/dubbo-dubbo-2.6.0/dubbo-admin/target/dubbo-admin-2.6.0.war .
mv dubbo-admin-2.6.0.war admin/
unzip dubbo-admin-2.6.0.war -d webapps/ROOT
```

## 修改dubbo.properties文件

```bash
dubbo.registry.address=redis://127.0.0.1:6379
dubbo.admin.root.password=root
dubbo.admin.guest.password=root
```

## 启动

```bash
docker run -d -p 8888:8080 -v "$PWD/webapps":/usr/local/tomcat/webapps/ --net redis_default --link redis_redis_1:redis tomcat:8.5.28-jre8-alpine
```

## 访问

http://192.168.99.100:8888

![](https://xuxifu-blog-img.oss-cn-beijing.aliyuncs.com/post/dubbo/image/20180302_admin.png)