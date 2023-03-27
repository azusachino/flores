---
title: Instruction 2023
created: 2022-08-28 00:00:00
modified: 2023-03-27 09:57:46
tags: [Collection, Instruction]
---

## Windows shutdown

```sh
# shutdown in one hour
shutdown -s -t 3600
# cancel shutdown
shutdown -a
```

## Java Pom

通过 mvn 命令，统一修改整个项目的版本信息

```sh
mvn versions:set -DnewVersion=1.0-dev
# 删除生成的 备份文件
find ./ -name pom.xml.versionsBackup | xargs rm
```

查看当前文件的 dep tree

```sh
mvn -f pom.xml dependency:tree
```

## SwitchyOmega

using auto proxy mode, set to `https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt`

## Grafana

Reset password with cli

`sudo grafana-cli admin reset-admin-password pwd123`

## Memos

`podman run -d --name memos -p 5230:5230 -v /iris/memos:/var/opt/memos docker.io/neosmemo/memos:latest`

## Rust

### compile openssl in windows

1. 克隆 vcpkg
2. 打开您克隆 vcpkg 的目录
3. 运行./bootstrap-vcpkg.bat
4. 运行 ./vcpkg.exe install openssl-windows:x64-windows
5. 运行 ./vcpkg.exe install openssl:x64-windows-static
6. 运行 ./vcpkg.exe 集成安装
7. 运行 set VCPKGRS_DYNAMIC=1(或简单地将其设置为您的环境变量)

## Format all files in vscode

1. install extension `Format files`
2. `Crtl+Shift+P` and select `Start to format`

## References

- [windows - 如何在 Windows 开发环境中使用 OpenSSL for Rust](https://www.coder.work/article/976641)
