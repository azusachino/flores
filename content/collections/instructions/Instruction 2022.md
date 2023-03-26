---
title: Instruction 2022
created: 2022-08-25 00:00:00
modified: 2023-02-15 09:04:01
tags: [Collection, Instruction]
---

## VaultWarden Setup

```sh
# 1. start and registration
podman run -d --name vaultwarden -v /iris/vaultwarden/data:/data -p 8081:80 vaultwarden/server:latest
# 2. remove the container 
podman stop && podman remove
# 3. start with no_signup option
podman run -d --name vaultwarden  -e SIGNUPS_ALLOWED=false -e ADMIN_TOKEN=azusachino1204  -v /iris/vaultwarden/data:/data -p 8081:80 vaultwarden/server:latest
```

## Anki-sync-server Setup

```sh
# 1. Create the container with default user/pass (可能在哪里初始化)
 podman create \
    --name=anki \
    -p 27701:27701 \
    -v /iris/anki/config:/config \
    -e ANKI_SYNC_SERVER_USER=azusachino@proton.me \
    -e ANKI_SYNC_SERVER_PASSWORD=azusachino.me \
    -e UID=1000 \
    -e GID=1000 \
    --restart unless-stopped \
    johngong/anki-sync-server:latest
# 2. 启动/停止
podman start/stop anki
```

### Windows setup addon

Create a new directory in [the add-ons folder](https://addon-docs.ankiweb.net/addon-folders.html) (name it something like ankisyncd), create a file named `__init__.py` containing the code below and put it in the `ankisyncd` directory.

```py
import os

addr = "http://127.0.0.1:27701/" # put your server address here
os.environ["SYNC_ENDPOINT"] = addr + "sync/"
os.environ["SYNC_ENDPOINT_MEDIA"] = addr + "msync/"
```

## mediawiki

```sh
# 1. initialize the database & config
podman run -d --name mediawiki -p 8080:80 docker.io/mediawiki:lts
# 2. copy sqlite files out
MEDIA_ID=$(podman ps | grep mediawiki | awk '{print $1}')
podman cp $MEDIA_ID:/var/www/data/ /iris/mediawiki
podman /iris/mediawiki/LocalSettings.php $MEDIA_ID/var/www/html/LocalSettings.php
# 3. stop the old one
podman stop $MEDIA_ID && podman rm $MEDIA_ID
# 4. load the LocalSettings 
podman run -d --name mediawiki -p 8080:80 \
-v /iris/mediawiki/html/images:/var/www/html/images \
docker.io/mediawiki:lts
```

Final Solution:

```sh
podman run -d --name mediawiki -p 8080:80 \
-v /iris/mediawiki/html/images:/var/www/html/images \
docker.io/mediawiki:lts
MEDIA_ID=$(podman ps | grep mediawiki | awk '{print $1}')
podman cp /iris/mediawiki/html/LocalSettings.php $MEDIA_ID:/var/www/html/LocalSettings.php
podman restart $MEDIA_ID
```

## Caddy

```sh
caddy start --config /iris/caddy/Caddyfile --adapter caddyfile
```

目前在用的Caddyfile

```caddy
azusachino.icu {
    root *  /usr/share/nginx/html
    file_server
    tls azusachino@proton.me
}

record.azusachino.icu {
    root *  /home/ycpang/flos/record
    file_server
    tls azusachino@proton.me
}

secret.azusachino.icu {
    reverse_proxy 127.0.0.1:8081
    tls azusachino@proton.me
}

anki.azusachino.icu {
    reverse_proxy 127.0.0.1:27701
    tls azusachino@proton.me
}

sync.azusachino.icu {
    reverse_proxy http://127.0.0.1:8384 {
            header_up Host {upstream_hostport}
            header_up X-Forwarded-Host {host}
    }
}
```

### naiveproxy

**Installation** build caddy with naiveproxy-plugin

```sh
go install github.com/caddyserver/xcaddy/cmd/xcaddy@latest
~/go/bin/xcaddy build --with github.com/caddyserver/forwardproxy@caddy2=github.com/klzgrad/forwardproxy@naive
```

[ref](https://github.com/klzgrad/naiveproxy)

`caddy start --config /etc/caddy/Caddyfile --adapter caddyfile`

```sh
:443, miku.azusachino.icu
tls azusachino@proton.me
route {
  forward_proxy {
    basic_auth haru password
    hide_ip
    hide_via
    probe_resistance
  }
  root * /var/www/html
  file_server
}
```

## SSH

make sure your `~/.ssh` is not over `600`.

```
# 1. setup authorized_keys
# 2. config `/etc/ssh/sshd_config`
vim /etc/ssh/sshd_config
  # Enable PubKey, RSA
    PermitRootLogin yes # 允许root登录
    PubkeyAuthentication yes # 允许通过公钥验证用户
    RSAAuthentication yes # 通过RSA算法验证
# 3. restart sshd
systemctl restart sshd
```

## Install Docker

```
# 1. check remaining stuffs and clean
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2. add yum-utils & configure yum-repo
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
# 3. directly install with yum
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
# 4. start docker
systemctl start docker
# 5. possible auto-start
systemctl enable docker
```

## Compile VIM with python3 support in centos8

Should remove yum-installed vim before hand.

```sh
# 1. clone the repo
git clone git@github.com:vim/vim
# 2. install deps
yum install ncurses-devel python3-devel
# 3. configure python3
cd vim/src
./configure --enable-python3interp --with-python3-config-dir=/usr/lib64/python3.6/config-3.6m-x86_64-linux-gnu --enable-rubyinterp --enable-gui=no --without-x --enable-cscope --enable-multibyte --prefix=/usr/local
# 4. make install
make && make install
```

## Taskfile

```sh
# unix
go install github.com/go-task/task/v3/cmd/task@latest
# macos
brew install go-task
```

```yml
version: '3'
dotenv: ['.env']
tasks:
  build:
    cmds:
      - mvn clean install -DskipTests
```

## References

- [docker 镜像列表](https://github.com/gshang2017/docker)
