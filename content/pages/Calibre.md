---
title: Calibre
created: 2023-02-21 08:00:00
modified: 2023-03-27 10:23:57
tags: [Tool, Reading]
---

An e-book library manager.

## Installations

```sh
# calibre server
docker pull linuxserver/calibre:latest

docker run -d \
  --name=calibre \
  --security-opt seccomp=unconfined `#optional` \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Etc/UTC \
  -e PASSWORD= `#optional` \
  -e CLI_ARGS= `#optional` \
  -p 8080:8080 \
  -p 8081:8081 \
  -v /path/to/data:/config \
  --restart unless-stopped \
  lscr.io/linuxserver/calibre:latest
```

## References

- [Calibre user Manual](https://manual.calibre-ebook.com/)
- [How to Use Calibre for Kindle – Step By Step Guide](https://techwiser.com/use-calibre-guide-for-kindle/)
- [How To Organize Your Ebook Collection with Calibre](https://www.howtogeek.com/73979/how-to-organize-your-ebook-collection-with-calibre)

## Resources
