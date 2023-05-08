---
title: System Design
created: 2022-09-01 16:00:00
modified: 2023-03-27 10:29:29
aliases: [系统设计]
tags: [CS]
---

Learn how to design systems at scale and prepare for system design interviews.

## Primitives

- Load balancer
- Gateway (rate-limiting, authentication)
- Database
  - SQL vs NoSQL, sharding strategy
  - if SQL, indices
- Cache (optionally clustered/sharded)
- Message queue, with workers
- CDN
- Lambdas

## Checklist

- Clarify requirements
  - Write QPS, average and peak (peak is 2x average)
  - Read QPS (approximate 10x write QPS)
  - Total storage required
  - Latency requirement
  - Consistency vs Availability
- High level diagram that matches requirements
- Deep Dive
  - APIs
  - Database schema
- Wrap up
  - Error cases
  - Potential bottlenecks
- Extra credit
  - Cost (development vs ongoing)
  - Logging, metrics, alerts
  - Automation - CI, CD
  - i18n
  - CDN
  - RP![[../images/Pasted image 20230423071407.png]]![[../images/Pasted image 20230423071407.png]]C

## Brief View

![[../images/system-design-brief-view.png]]

## Designs

### Rate Limiter

![[../images/rate-limiter.png]]

### KV Store

![[../images/kv-store-1.png]]

![[../images/kv-store-2.png]]

![[../images/kv-store-3.png]]

### Tiny URL Mechanism

![[../images/tiny-url.png]]

### Web Crawler

![[../images/web-crawler.png]]

### Notification Service

![[../images/notification-service.png]]

### News Feed

![[../images/news-feed.png]]

### Chat Service

![[../images/chat-service.png]]

Another One:

![[../images/chat-application.png]]

### Another Chat Application

![[../images/system-design-chat-application.png]]

### Permission System

![[../images/permission-system.png]]

## References

- [System Design - github](https://github.com/karanpratapsingh/system-design)
- [System Design Interview](https://til.nindalf.com/books/system-design-interview/)
- [System Design Primer - github](https://github.com/donnemartin/system-design-primer)
- [EP18: Build a chat application](https://blog.bytebytego.com/p/ep18-build-a-chat-application-also)
- [EP24: Environment-friendly languages](https://blog.bytebytego.com/p/ep24-environment-friendly-languages)
