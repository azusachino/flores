---
title: Google Chrome
created: 2022-11-21 22:08:58
modified: 2022-12-05 14:05:28
tags: [Tool]
---

## 解决证书无效，无法访问

1. 添加自用的CA证书到 证书管理器里
2. 在Chrome提示"您的连接不是私密连接"页面的空白区域点击一下，然后输入"thisisunsafe"(页面不会有任何输入提示)，输入完成后会自动继续访问。

## References

- [Google Chrome访问出现 NET::ERR_CERT_INVALID](https://blog.csdn.net/MrFDd/article/details/123302905)
