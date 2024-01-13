---
title: D-Eyes常用命令
date: 2024-01-07 09:58:04
tags: 渗透测试
mathjax: true
---

# D-Eyes常用命令

## 文件扫描

```bash
D-Eyes fs #全盘扫描
D-Eyes fs -t 8 #8进程
D-Eyes fs -P /kali -t 8 #8进程 指定目录
```

## 进程扫描

```bash
D-Eyes ps #默认扫描
D-Eyes ps -p 8888 #指定pid
```

## 信息搜集

```bash
D-Eyes host #查看主机信息
D-Eyes top #查看前15进程
D-Eyes sc #主机自检：空密码账户、SSh Server wrapper、SSH免密证书登录、Sudoer、alias、setuid、SSH登录爆破等
```

