---
title: "Multicast registry center"
date: 2018-03-05T14:51:46+08:00
tags: ["dubbo", "Multicast registry"]
draft: false
---

------------------

# Multicast 注册中心

Multicast 注册中心不需要启动任何中心节点，只要广播地址一样，就可以互相发现。

![](https://xuxifu-blog-img.oss-cn-beijing.aliyuncs.com/post/dubbo/image/20180302_multicast.png)

1. 提供方启动时广播自己的地址
2. 消费方启动时广播订阅请求
3. 提供方收到订阅请求时，单播自己的地址给订阅者，如果设置了 `unicast=false`，则广播给订阅者
4. 消费方收到提供方地址时，连接该地址进行 RPC 调用。

组播受网络结构限制，只适合小规模应用或开发阶段使用。组播地址段: 224.0.0.0 - 239.255.255.255



## 配置

```
<dubbo:registry address="multicast://224.5.6.7:1234" />

```

或

```
<dubbo:registry protocol="multicast" address="224.5.6.7:1234" />

```

为了减少广播量，Dubbo 缺省使用单播发送提供者地址信息给消费者，如果一个机器上同时启了多个消费者进程，消费者需声明 `unicast=false`，否则只会有一个消费者能收到消息：

```
<dubbo:registry address="multicast://224.5.6.7:1234?unicast=false" />

```

或

```
<dubbo:registry protocol="multicast" address="224.5.6.7:1234">
    <dubbo:parameter key="unicast" value="false" />
</dubbo:registry>
```

## 问题

如果出现“org.jboss.netty.channel.ConnectTimeoutException: connection timed out”

请禁用除本地网络以外的网络