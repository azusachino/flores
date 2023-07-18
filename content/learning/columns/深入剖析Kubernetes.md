---
title: 深入剖析Kubernetes
created: 2023-01-10 00:00:00
modified: 2023-07-18 07:55:49
tags: [Column, Tech, Kubernetes]
author: 张磊
read: 52
total: 52
status: done
---

**从过去以物理机和虚拟机为主体的开发运维环境，向以容器为核心的基础设施的转变过程，并不是一次温和的改革，而是涵盖了对网络、存储、调度、操作系统、分布式原理等各个方面的容器化理解和改造。**

## Daemon Set

利用 Kubernetes 提供的 taint 机制实现，默认会添加数个 toleration

- `node.kubernetes.io/unschedulable`
- `node.kubernetes.io/network-unavailable`

## RBAC

### Namespace

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: iris
  name: iris-role
rules:
  - apiGroups: [""]
    resource: ["pods"]
    verbs: ["get", "watch", "list"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-rolebinding
  namespace: mynamespace
subjects:
- kind: User
  name: example-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: example-role
  apiGroup: rbac.authorization.k8s.io
```

### Non-Namespace

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-clusterrole
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-clusterrolebinding
subjects:
- kind: User
  name: example-user
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: example-clusterrole
  apiGroup: rbac.authorization.k8s.io
```

## 评价

不是特别深入，只能算是导论。

## References

- [深入剖析Kubernetes - 极客时间](http://localhost/#)
