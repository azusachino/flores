---
title: Instruction 2023
created: 2022-08-28 16:00:00
modified: 2023-04-09 15:58:24
tags: [Collection, Instruction]
---

## Windows shutdown

```sh
# shutdown in one hour
shutdown -s -t 3600
# cancel shutdown
shutdown -a
```

## Maven

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

update deps version

```sh
# Displaying Available Updates (heavy operation, not recommend)
mvn versions:display-dependency-updates

# Converting SNAPSHOTs into RELEASEs
mvn versions:use-releases

# Updating to the Next RELEASE
mvn versions:use-next-releases

# Updating to the Latest RELEASE
mvn versions:use-latest-releases
```

## SwitchyOmega

using auto proxy mode, set to `https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt`

## Grafana

Reset password with cli

`sudo grafana-cli admin reset-admin-password pwd123`

## Memos

`podman run -d --name memos -p 127.0.0.1:5230:5230 -v /iris/memos:/var/opt/memos docker.io/neosmemo/memos:latest`

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

## Useful DNS Record

```sh
140.82.113.4    github.com
185.199.111.133 githubusercontent.com
```

## NVIM

```sh
winget install Neovim.Neovim

brew install neovim
yum install neovim
```

## Git Useful

```sh
# switch default editor
git config --global core.editor "vim"
```

### ZSH

install `sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

```sh
# useful plugins
plugins=(
git
history-substring-search
zsh-autosuggestions
zsh-syntax-highlighting
)
```

- install `zsh-autosuggestions`: `git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
- install zsh-syntax-highlighting, [repo](https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md)
  - mac: `brew install zsh-syntax-highlighting`
  - [centos](https://software.opensuse.org/download.html?project=shells%3Azsh-users%3Azsh-syntax-highlighting&package=zsh-syntax-highlighting)
    - `cd /etc/yum.repos.d/`
    - `wget https://download.opensuse.org/repositories/shells:zsh-users:zsh-syntax-highlighting/CentOS_7/shells:zsh-users:zsh-syntax-highlighting.repo`
    - `yum install zsh-syntax-highlighting`
  - `git clone https://github.com/zsh-users/zsh-syntax-highlighting.git`
  - `echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc`

## YUM

### Yum install local

`yum localinstall *.rpm`

## References

- [windows - 如何在 Windows 开发环境中使用 OpenSSL for Rust](https://www.coder.work/article/976641)
