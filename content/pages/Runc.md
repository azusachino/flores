---
title: runC
created: 2022-08-30 08:00:00
modified: 2023-03-26 11:19:47
tags: [CS, Microservices]
---

> [!info] runc
`runc` is a CLI tool for spawning and running containers on Linux according to the OCI specification.

容器运行时是指管理容器和容器镜像的软件。

## 如何使用

### 创建 OCI Bundle

OCI Bundle 指满足OCI标准的一系列文件，这些文件包含了运行容器所需要的所有数据，存放在一个共同的目录，包含一下两项：

- `config.json` 容器运行的配置
- `rootfs` 容器的文件系统

参考步骤

```shell
# creater sample container directory
$ mkdir /ctnr
$ cd /ctnr

# create the rootfs
$ mkdir rootfs

# export busybox via Docker into the rootfs
$ docker export $(docker create busybox) | tar -C rootfs -xvf -

# check rootfs
$ ls rootfs 
bin  dev  etc  home  proc  root  sys  tmp  usr  var

# create template config.json
$ runc spec
$ ls
config.json rootfs
```

修改 `config.json` 以验证容器启动效果

```json
{

  "process": {
    // 启用终端
    "terminal": true,
    "user": {
      "uid": 0,
      "gid": 0
    },
    // 将初始化命令调整为 sleep 以查看效果
    "args": ["sleep 10"],
    "env": [
 "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
      "TERM=xterm"
    ],
    "cwd": "/"
  },
  "root": {
    "path": "rootfs",
    "readonly": true
  },
  "linux": {
    "namespaces": [
      {
        "type": "pid"
      },
      {
        "type": "network"
      },
      {
        "type": "ipc"
      },
      {
        "type": "uts"
      },
      {
        "type": "mount"
      }
    ]
  }
}
```

## 代码结构

![[../images/runc-project-structure.png]]

```go
// 基础配置信息
setupSpec(context) 
// 创建容器
startContainer(context, spec, CT_ACT_CREATE, nil)
createContainer
// 平台容器相关配置
specconv.CreateLibcontainerConfig
// containerFacotry
loadFactory(context)
libcontainer.New(……)
factory.Create(id, config)
// 运行用户指定的应用程序，如 `sleep 5`
runner.run(spec.Process) 
newProcess(*config, r.init) 
r.container.Start(process) 
c.createExecFifo() 
c.start(process) 
c.newParentProcess(process) 
parent.start()
```

## 问题排查

### Runc 启动失败

runc: symbol lookup error: runc: undefined symbol: seccomp_api_get

原因是缺少了 `libseccomp-devel`，且版本必须高于 2.4。Lib libseccomp 2.4 added seccomp_api_get.

## References

- [runC - github](https://github.com/opencontainers/runc)
- [runC(上) - switch router](https://switch-router.gitee.io/blog/runc-1/)
- [runC(下) - switch router](https://switch-router.gitee.io/blog/runc-2/)
