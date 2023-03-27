---
title: Kubernetes
created: 2022-08-30 08:00:00
modified: 2023-03-27 10:28:09
aliases: [k8s]
tags: [CS, Microservices]
---

Kubernetes is a software system for automating the deployment and management of complex, large-scale application systems composed of computer processes running in containers.

> [!info] Kubernetes
> Kubernetes 被定义成一个用于自动化部署、扩容和管理容器应用的开源系统；它将一个分布式软件的一组容器打包成一个个更容易管理和发现的逻辑单元。

## 四大特征

- Declarative - 声明式
- Explicated API - 显式接口
- No Invasion - 无侵入性
- Portability - 可移植性

## 特性能力

- 自我修复：一旦某一个容器崩溃，能够在 1 秒左右启动新的容器 `Deployment`
- 弹性伸缩：可用根据配置，自动对集群中正在运行的容器数量进行调整 `Horizontal Pod Autoscaler`
- 服务发现：服务可以通过自动发现的形式找到它所依赖的服务 `Kube-Proxy`
- 负载均衡：`Service`
- 版本回退：`Deployment` 提供的发布版本控制能力
- 存储编排：`PV`，`PVC`

## 核心组件

- API Server：资源操作的唯一入口，接受用户输入的命令，提供认证、授权、API 注册和发现等机制
- Kube-Scheduler：集群资源调度，按照预定的调度策略将 Pod 调度到合适的 Node 节点上
- (Cloud) Controller Manager：服务维护集群的状态，如程序部署安排、故障检测、自动扩展、滚动更新等
- [[Etcd]]：存储集群中各种资源对象的信息
- Node\*\*：集群的数据平面，为容器提供运行环境
- Kubelet：管理容器的生命周期，通过 docker-shim/containerd 组件来创建、更新、销毁容器
- Kube-Proxy：提供集群内部的服务发现和负载均衡
- [[Docker]] (或其他替代品): 容器相关操作

![[../images/k8s-structure.png]]

### Pod 创建流程

**在 Kubernetes 环境启动之后，Master 节点和 Node 节点都会将自身的信息存储到 etcd**

- 客户端提交 Pod 的配置信息（可以是 yaml 文件定义好的信息）到 kube-apiserver；
- Apiserver 收到指令后，通知给 controller-manager 创建一个资源对象；
- Controller-manager 通过 api-server 将 pod 的配置信息存储到 ETCD 数据中心中；
- Kube-scheduler 检测到 pod 信息会开始调度预选，会先过滤掉不符合 Pod 资源配置要求的节点，然后开始调度调优，主要是挑选出更适合运行 pod 的节点，然后将 pod 的资源配置单发送到 node 节点上的 kubelet 组件上。
- Kubelet 根据 scheduler 发来的资源配置单运行 pod，运行成功后，将 pod 的运行信息返回给 scheduler，scheduler 将返回的 pod 运行状况的信息存储到 etcd 数据中心。

`Nginx-deployment.yml` 案例

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
      type: NodePort # for external usage
```

## 基础概念

- **Master**：集群控制节点，每个集群需要至少一个 master 节点来负责集群的管控。
- **Node**：工作负载节点，由 master 分配容器到这些 node 工作节点上，然后 node 节点上的 docker 负责容器的运行。
- **Pod**：Kubernetes 的最小控制单元，容器都是运行在 pod 中的，一个 pod 中可以有 1 个或者多个容器。
- **Controller**：控制器，通过它来实现对 pod 的管理，比如启动 pod、停止 pod、伸缩 pod 的数量等等。
- **Service**：pod 对外服务的统一入口，下面可以维护者同一类的多个 pod。
- **Label**：标签，用于对 pod 进行分类
- **Name Space**：命名空间，用来隔离 k8s 的各类资源

### Pod

Pod 的创建都是通过  `SyncPod`  来实现的，创建的过程大体上可以分为六个步骤：

1. 计算 Pod 中沙盒和容器的变更；
2. 强制停止 Pod 对应的沙盒；
3. 强制停止所有不应该运行的容器；
4. 为 Pod 创建新的沙盒；
5. 创建 Pod 规格中指定的初始化容器；
6. 依次创建 Pod 规格中指定的常规容器；

#### 容器启动

1. 通过镜像拉取器获得当前容器中使用镜像的引用；
2. 调用远程的  `runtimeService`  创建容器；
3. 调用内部的生命周期方法  `PreStartContainer`  为当前的容器设置分配的 CPU 等资源；
4. 调用远程的  `runtimeService`  开始运行镜像；
5. 如果当前的容器包含  `PostStart`  钩子就会执行该回调；

#### Pod Network

Each node has a network interface - `eth0` - attached to the Kubernetes cluster network. This interface sits within the node's `root` network namespace. This is the default namespace for networking devices on Linux. Network namespaces isolate devices such as interfaces and bridges. **Each pod on a node is assigned its own isolated network namespace**.

Pod namespaces are connected back to the `root` namespace with a _virtual ethernet pair_, essentially a pipe between the two namespaces with an interface on each end (here we're using **veth1** in the **root** namespace, and **eth0** within the pod).

Pods are connected to each other and to the node's `eth0` interface via a bridge, `br0` (your node may use something like `cbr0` or `docker0`). A bridge essentially works like a physical ethernet switch, using either ARP()

![[../images/k8s-pod-network.png]]

## 一些知识点

### Pod 与 Container

![[../images/pod-container-structure.png]]

容器和 Pod 是相似的。在底层，它们主要依赖 Linux 命名空间和 cgroup。 但是，Pod 不仅仅是一组容器。 Pod 是一个自给自足的高级构造。 所有 Pod 的容器都运行在同一台机器（集群节点）上，它们的生命周期是同步的，并且通过削弱隔离性来简化容器间的通信。

当我们执行 `kubectl exec (POD | TYPE/NAME) [-c CONTAINER] [flags] -- COMMAND [args...] [options]` 的时候，实际上就是切换到目标 namespace，就可以读取 Pod 内部共享的信息

## Development

### Troubleshooting Kubernetes Deployments

![[../images/troubleshoot-k8s-deployment.png]]

## References

- [Kubernetes Documentation](https://kubernetes.io/docs/home/)
- [Kubernetes The Hard Way](https://github.com/kelseyhightower/kubernetes-the-hard-way)
- [谈 Kubernetes 的架构设计与实现原理](https://draveness.me/understanding-kubernetes/)
- [深入 k8s](https://www.luozhiyun.com/archives/tag/%e6%b7%b1%e5%85%a5k8s)
- [ ] [Understanding kubernetes networking: pods](https://medium.com/google-cloud/understanding-kubernetes-networking-pods-7117dd28727)
- [Containers vs. Pods - Taking a Deeper Look](https://iximiuz.com/en/posts/containers-vs-pods/)
  - 利用示例，解释了容器和 Pod 之间的关系
- [Kubernetes in Action](https://wangwei1237.github.io/Kubernetes-in-Action-Second-Edition/docs)
