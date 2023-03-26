---
title: Cloud Native
created: 2022-08-27 18:34:03
modified: 2022-12-05 14:05:28
aliases: [云原生]
tags: [CS, Cloud]
---

云原生技术有利于各组织再公有云、私有云和混合云等新型动态环境中，构建和运行可弹性扩展的应用。云原生的代表技术包括[[Container|容器]]、[[Service Mesh|服务网格]]、[[Microservices|微服务]]、不可变基础设施和声明式接口。

## 一些特点

- 通过[[Container|容器]]来实现不可变的基础设施
- 向应用代码隐藏环境复杂度
- [[Docker]]: Build Once, Run Anywhere
- 微服务追求不一致性：选用多种语言和不同框架来实现不同的应用
- 微服务拆分：不需要管理巨型内存
- 容器化部署：对镜像体积、内存消耗、启动速度有更高要求
- 分布式集群：不强调单机高可用
- Function：应用颗粒度拆分到极致
- Serverless：无需长期运行，按需上线/下线

## References

- [CNCF Landscape](https://landscape.cncf.io/)
