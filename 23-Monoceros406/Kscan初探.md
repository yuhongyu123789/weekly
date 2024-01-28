---
title: Kscan初探
date: 2024-01-26 22:31:00
tags: Kscan
mathjax: true
---

# Kscan初探

端口扫描：

```bash
kscan -t 192.168.217.1/24
```

存活网段探测：

```bash
kscan --spy
```

FOFA检索：

```bash
kscan -f 'title="后台管理"' --fofa-size 15
```

暴力破解：

```bash
kscan -t 127.0.0.1 --hydra
```

CDN识别：

```bash
kscan -t www.iywaobt.cn
```
