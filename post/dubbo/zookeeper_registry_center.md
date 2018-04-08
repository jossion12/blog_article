---
title: "Zookeeper registry center"
date: 2018-03-05T15:48:46+08:00
tags: ["dubbo", "Zookeeper registry"]
draft: false
commentid: "Zookeeper-registry-center"
---

------------------

# zookeeper 注册中心

[Zookeeper](http://zookeeper.apache.org/) 是 Apacahe Hadoop 的子项目，是一个树型的目录服务，支持变更推送，适合作为 Dubbo 服务的注册中心，工业强度较高，可用于生产环境，并推荐使用

![](https://xuxifu-blog-img.oss-cn-beijing.aliyuncs.com/post/dubbo/image/20180302_zookeeper.png)

流程说明：

- 服务提供者启动时: 向 `/dubbo/com.foo.BarService/providers` 目录下写入自己的 URL 地址
- 服务消费者启动时: 订阅 `/dubbo/com.foo.BarService/providers` 目录下的提供者 URL 地址。并向 `/dubbo/com.foo.BarService/consumers` 目录下写入自己的 URL 地址
- 监控中心启动时: 订阅 `/dubbo/com.foo.BarService` 目录下的所有提供者和消费者 URL 地址。

支持以下功能：

- 当提供者出现断电等异常停机时，注册中心能自动删除提供者信息
- 当注册中心重启时，能自动恢复注册数据，以及订阅请求
- 当会话过期时，能自动恢复注册数据，以及订阅请求
- 当设置 `<dubbo:registry check="false" />` 时，记录失败注册和订阅请求，后台定时重试
- 可通过 `<dubbo:registry username="admin" password="1234" />` 设置 zookeeper 登录信息
- 可通过 `<dubbo:registry group="dubbo" />` 设置 zookeeper 的根节点，不设置将使用无根树
- 支持 `*` 号通配符 `<dubbo:reference group="*" version="*" />`，可订阅服务的所有分组和所有版本的提供者

## 使用

在 provider 和 consumer 中增加 zookeeper 客户端 jar 包依赖：

```
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.3.3</version>
</dependency>

```

或直接[下载](http://repo1.maven.org/maven2/org/apache/zookeeper/zookeeper)。

Dubbo 支持 zkclient 和 curator 两种 Zookeeper 客户端实现：

### 使用 zkclient 客户端

从 `2.2.0` 版本开始缺省为 zkclient 实现，以提升 zookeeper 客户端的健状性。[zkclient](https://github.com/sgroschupf/zkclient) 是 Datameer 开源的一个 Zookeeper 客户端实现。

缺省配置：

```
<dubbo:registry ... client="zkclient" />

```

或：

```
dubbo.registry.client=zkclient

```

或：

```
zookeeper://10.20.153.10:2181?client=zkclient

```

需依赖或直接[下载](http://repo1.maven.org/maven2/com/github/sgroschupf/zkclient)：

```
<dependency>
    <groupId>com.github.sgroschupf</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.1</version>
</dependency>
```

### 使用 curator 客户端

从 `2.3.0` 版本开始支持可选 curator 实现。[Curator](https://github.com/Netflix/curator) 是 Netflix 开源的一个 Zookeeper 客户端实现。

如果需要改为 curator 实现，请配置：

```
<dubbo:registry ... client="curator" />

```

或：

```
dubbo.registry.client=curator

```

或：

```
zookeeper://10.20.153.10:2181?client=curator

```

需依赖或直接[下载](http://repo1.maven.org/maven2/com/netflix/curator/curator-framework)：

```
<dependency>
    <groupId>com.netflix.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>1.1.10</version>
</dependency>

```

Zookeeper 单机配置:

```
<dubbo:registry address="zookeeper://10.20.153.10:2181" />

```

或：

```
<dubbo:registry protocol="zookeeper" address="10.20.153.10:2181" />

```

Zookeeper 集群配置：

```
<dubbo:registry address="zookeeper://10.20.153.10:2181?backup=10.20.153.11:2181,10.20.153.12:2181" />

```

或：

```
<dubbo:registry protocol="zookeeper" address="10.20.153.10:2181,10.20.153.11:2181,10.20.153.12:2181" />
```

