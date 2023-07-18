---
title: Kubernetes入门实战课
created: 2023-01-09 08:00:00
modified: 2023-07-18 13:31:24
tags: [Column, Tech, Kubernetes]
author: 罗剑锋
read: 28
total: 28
status: done
---

容器就是被隔离的进程。容器共享了宿主机的内核。

![[../../images/virtual-machine-vs-container.png]]

## Three Pillar

- Linux Namespace
- Linux Control Group
- Chroot / `pivot_root`

## Dockerfile

仅有 RUN, COPY, ADD 会生成新的镜像层，其他指令只会产生临时层，不影响构建大小。

## Docker 知识概览

![[../../images/docker-knowledge-base.png]]

## K8S Arch

![[../../images/kubernetes-basic-arch.png]]

![[../../images/kubernetes-basic-arch-2.png]]

## K8S APIs

![[../../images/kubernetes-api-objects.png]]

## Kubernetes

### Deployment

若将 replicas 字段设置为 0，可以在关闭服务的同时，保留服务的相关配置；若需要再次启动，可以直接使用 scale up。

### PV & PVC

- PVC 是申请动作，用于指明 需要什么样的 PV
- PV 就是 Volume，用户在 apply pvc 时会从系统中已存在的PV中寻找匹配的，并进行绑定

![[../../images/kubernetes-pod-pvc-pv.png]]

## References

- [Kubernetes入门实战课 - 极客时间](http://localhost)
