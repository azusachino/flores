---
title: Centos
created: 2022-08-26 16:00:00
modified: 2023-03-27 10:24:09
tags: [Linux, OS]
---

> [!info] CentOS
> CentOS is a Linux distribution that provides a free and open-source community-supported computing platform, functionally compatible with its upstream source, Red Hat Enterprise Linux.

目前国内常用的版本是 Centos7，将在 2024 年停止维护。

## 查看 Centos 版本

```shell
# 查询版本信息
cat /etc/redhat-release

uname -a #查看内核版本等信息

cat /proc/cpuinfo | grep "physical id" | uniq | wc -l #查看cpu个数

cat /proc/cpuinfo | grep "cpu cores" | uniq #查看cpu核数

cat /proc/cpuinfo | grep 'model name' |uniq #查看cpu型号
```

## Centos(7|8) (x86_64) 配置源 [标准 Centos]

```shell
# 对于 CentOS 7
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org|baseurl=https://mirrors.tuna.tsinghua.edu.cn|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo

# 对于 CentOS 8
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/$contentdir|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo

# 更新缓存
sudo yum makecache
```

## Centos 非 x86_64 架构 [centos-altarch]

```shell
sudo sed -e 's|^mirrorlist=|#mirrorlist=|g' \
         -e 's|^#baseurl=http://mirror.centos.org/altarch/|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-altarch/|g' \
         -e 's|^#baseurl=http://mirror.centos.org/$contentdir/|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-altarch/|g' \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo
# 更新缓存
sudo yum makecache
```

## 已不维护的 Centos [centos-vault]

如 Centos8 ARM 架构

```shell
# CentOS 8 之前
minorver=6.10
sudo sed -e "s|^mirrorlist=|#mirrorlist=|g" \
         -e "s|^#baseurl=http://mirror.centos.org/centos/\$releasever|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/$minorver|g" \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo

# CentOS 8 之后
minorver=8.5.2105
sudo sed -e "s|^mirrorlist=|#mirrorlist=|g" \
         -e "s|^#baseurl=http://mirror.centos.org/\$contentdir/\$releasever|baseurl=https://mirrors.tuna.tsinghua.edu.cn/centos-vault/$minorver|g" \
         -i.bak \
         /etc/yum.repos.d/CentOS-*.repo
# 更新缓存
sudo yum makecache
```

### 一键获取阿里云地址

`wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo`

## Install Local Rpm

```sh
# install package with deps
yum --nogpgcheck localinstall packagename.arch.rpm

# or use rpm
rpm -i your.rpm
```

## Useful Links

- [清华源](https://mirrors.tuna.tsinghua.edu.cn/)
- [中科大源](https://mirrors.ustc.edu.cn/)
- [Aliyun Mirror](https://developer.aliyun.com/mirror/centos)
