---
title: Trick 2023
created: 2022-09-19 16:00:00
modified: 2023-07-18 11:45:50
tags: [Collection, Trick]
---

## 无线上网更加舒畅

- 改变信道，解决 Wi-Fi 信号拥堵
    - netspot
- 使用 QoS，智能调节设备带宽
- 整理 WiFi 列表
    - 展示所有 wifi  `netsh wlan show networks`
    - 隐藏指定 wifi `netsh wlan add filter permission=block ssid="这里填上你想要隐藏的 Wi-Fi 名字" networktype=infrastructure`
    - 白名单、黑名单 `netsh wlan show filters`
        - `netsh wlan add filter permission=allow ssid="自家的 Wi-Fi 名字" networktype=infrastructure`
        - `netsh wlan add filter permission=denyall networktype=infrastructure`
- 注意路由器天线的摆放方向 【与地面垂直】
- 定时重启路由器
