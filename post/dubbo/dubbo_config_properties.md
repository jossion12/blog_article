---
title: "Dubbo Config Properties"
date: 2018-02-20T21:07:46+08:00
tags: ["Dubbo config","properties"]
draft: false
---

------------------

# Dubbo Properties配置

## 配置方式

### Properties配置

* 如果公共配置很简单，没有多注册中心，多协议等情况，或者想多个 Spring 容器想共享配置，可以使用 dubbo.properties 作为缺省配置。
* Dubbo 将自动加载 classpath 根目录下的 dubbo.properties，可以通过JVM启动参数 `-Ddubbo.properties.file=xxx.properties` 改变缺省配置位置。

### 映射规则

* 将 XML 配置的标签名，加属性名，用点分隔，多个属性拆成多行
  * 比如：`dubbo.application.name=foo`等价于`<dubbo:application name="foo" />`
  * 比如：`dubbo.registry.address=10.20.153.10:9090`等价于`<dubbo:registry address="10.20.153.10:9090" />`
* 如果 XML 有多行同名标签配置，可用 id 号区分，如果没有 id 号将对所有同名标签生效
  * 比如：`dubbo.protocol.rmi.port=1234`等价于`<dubbo:protocol id="rmi" name="rmi" port="1099" />`
  * 比如：`dubbo.registry.china.address=10.20.153.10:9090`等价于`<dubbo:registry id="china" address="10.20.153.10:9090" />`

### 覆盖策略

![](post/dubbo/image/20180302_properties_covert.png)

JVM 启动 -D 参数优先，这样可以使用户在部署和启动时进行参数重写，比如在启动时需改变协议的端口。

XML 次之，如果在 XML 中有配置，则 dubbo.properties 中的相应配置项无效。

Properties 最后，相当于缺省值，只有 XML 没有配置时，dubbo.properties 的相应配置项才会生效，通常用于共享公共配置，比如应用名。