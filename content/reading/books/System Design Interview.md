---
title: System Design Interview, An Insider's Guide
created: 2023-01-08 01:34:23
modified: 2023-03-26 11:19:48
started: 2023-02-13
finished: 2023-04-30
read: 1
total: 16
author: [Alex Xu]
tags: [Book, CS, SystemDesign]
---

## General Architecture

![[../../images/system-design-architecture.png]]

### Suggestions for millions of users and beyond

1. Keep web tier stateless
2. Build redundancy at every tier
3. Cache data as much as you can
4. Support multiple data centers
5. Host static assets in CDN
6. Scale your data tier by sharding
7. Split tiers into individual services
8. Monitor your system and use automation tools

## References

- [bytebytego](https://blog.bytebytego.com/)
