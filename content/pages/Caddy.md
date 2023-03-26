---
title: Caddy
created: 2022-08-21 00:00:00
modified: 2022-12-05 14:05:28
tags: [Network, Golang]
---

Caddy is a powerful, extensible platform to serve your sites, services, and apps, written in Go. If you're new to Caddy, the way you serve the Web is about to change.

## Regularly Used Commands

- `caddy run`
	- start caddy server (may load the `Caddyfile` in cwd)
	- `caddy run --config /path/to/Caddyfile`
	- `caddy run --watch` watch config file changes
- `caddy start` - start caddy server and have it run in the background
- `caddy stop` - stop the caddy process
- `caddy reload` - for a graceful config change
- `caddy adapt` - convert `Caddyfile` to json structure

## The Caddyfile

### With Compression

```Caddyfile
localhost

encode zstd gzip
templates
file_server browse
```

### Multiple Sites

```Caddyfile
:8080 {
respond "I am 8080"
}

:8081 {
respond "I am 8081"
}
:8082, :8083 {
  respond "I am 808*"
}
```

### Reverse Proxy

```Caddyfile
localhost

file_server
reverse_proxy /api/* 127.0.0.1:9005
```

### Environment Variables

```sh
export SITE_ADDRESS=localhost:9055
```

```Caddyfile
{$SITE_ADDRESS}

# also comments here

file_server
```

### Static Files

- `caddy file-server --listen :2015`
- `caddy file-server --browse` - display a file listing (without index)
- `caddy file-server --root ~/site` - use specific folder as site root

## References

- [Caddy Official](https://caddyserver.com/docs/)
