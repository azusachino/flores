---
title: Instruction 2023
created: 2022-08-29 00:00:00
modified: 2023-05-15 18:52:52
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

### With Plugins

```sh
/root/.config/nvim
├── init.lua
├── lua
│   └── plugins.lua
└── plugin # compiled stuffs
    └── packer_compiled.lua
```

1. add `require("plugins")` to `init.lua`
2. install `packer.vim` for plugin managements
   1. a `git clone --depth 1 https://github.com/wbthomason/packer.nvim ~/.local/share/nvim/site/pack/packer/start/packer.nvim`
   2. add custom plugins configuration to `plugins.lua`
3. run nvim & `:PackerSync`

```lua
-- init.lua
requre("plugins")

-- disable netrw at the very start of your init.lua (strongly advised)
vim.g.loaded_netrw = 1
vim.g.loaded_netrwPlugin = 1

-- set termguicolors to enable highlight groups
vim.opt.termguicolors = true

-- empty setup using defaults
require("nvim-tree").setup()

-- OR setup with some options
require("nvim-tree").setup({
  sort_by = "case_sensitive",
  renderer = {
    group_empty = true,
  },
  filters = {
    dotfiles = true,
  },
})
```

```lua
-- plugins.lua

return require('packer').startup(function()
  -- Packer can manage itself
  use 'wbthomason/packer.nvim'
  use {
    'nvim-tree/nvim-tree.lua',
    requires = {
      'nvim-tree/nvim-web-devicons', -- optional
    },
    config = function()
      require("nvim-tree").setup {}
    end
  }
end)
```

## Git Useful

```sh
# switch default editor
git config --global core.editor "vim"
```

configure proxy for specific host

```sh
Host github.com
  HostName github.com
  User azusachino
  IdentityFile ~\.ssh\id_rsa_github
  PreferredAuthentications publickey
  ProxyCommand connect -S localhost:30808 %h %p
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
  - `git clone https://github.com/zsh-users/zsh-syntax-highlighting`
  - `echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc`

## YUM

### Yum install local

`yum localinstall *.rpm`

## HomeBrew

### Tuna

ref: `https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/`

```sh
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_API_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles/api"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles"
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_PIP_INDEX_URL="https://pypi.tuna.tsinghua.edu.cn/simple"
```

update local homebrew config

```sh
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
for tap in core cask{,-fonts,-drivers,-versions} command-not-found services; do
    brew tap --custom-remote --force-auto-update "homebrew/${tap}" "https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-${tap}.git"
done
```

## Linux

kill all my processes who is zombie.

```sh
ps -ef | grep ycpang | awk '{if($3==1) {print $2}}' | xargs kill -9
```

## References

- [windows - 如何在 Windows 开发环境中使用 OpenSSL for Rust](https://www.coder.work/article/976641)
