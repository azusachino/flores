---
title: Git
created: 2022-08-23 00:00:00
modified: 2023-03-27 10:25:25
tags: [CS, Tool]
---

> [!important]
> 当今世界最流行的版本管理工具。

## Instructions

### 更新远程分支列表

`git fetch --all`

仓库的分支已删除，而本地尚留存 `remotes/origin/**`，执行下述命令进行清理。

```shell
git remote update origin --prune
```

### Git switch tags

```sh
git checkout [origin] tags/v1.0
```

### Git using proxy

```sh
# http(s).proxy
git config --global http.proxy http://proxy.example.com:8080
git config --global https.proxy https://proxy.example.com:8080

# socks proxy
git config --global socks.proxy socks5://proxy.example.com:1080
```

### SSH-KEYGEN

```sh
# -t specifies the algorithm
# -b specifies the buffer size
ssh-keygen -t rsa -b 2048
```

### Recall a git merge

`git merge --abort`

## References

- [Git](https://git-scm.com/)
