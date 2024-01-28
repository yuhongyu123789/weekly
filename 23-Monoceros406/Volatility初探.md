---
title: Volatility初探
date: 2024-01-19 18:41:55
tags: 取证
mathjax: true
---

# Volatility初探

寻找profile：

```bash
vol -f mem.vmem imageinfo
```

查看进程：

```bash
vol -f mem.vmem --profile=XXX pslist
```

进程转储：

```bash
vol -f mem.vmem --profile=XXX memdump -p 2012 -D ./
```

查看用户密码：

```bash
vol -f mem.vmem --profile=XXX hashdump
```

查看cmd进程：

```bash
vol -f mem.vmem --profile=XXX cmdscan
```

查找flag关键词：

```bash
vol -f mem.vmem --profile=XXX filescan | grep flag
```

文件转储：

```bash
vol -f mem.vmem --profile=XXX dumpfiles -Q 0X00000123456 -D ./
```

查看Windows窗口程序，显示10行内容：

```bash
vol -f mem.vmem --profile=XXX windows | grep flag -A 10
```

从注册表中提取LSA密钥信息：

```bash
vol -f mem.vmem --profile=XXX lsadump
```

列出注册表信息：

```bash
vol -f mem.vmem --profile=XXX hivelist
```

查看进程树：

```bash
vol -f mem.vmem --profile=XXX pstree
```

查看某个进程DLL：

```bash
vol -f mem.vmem --profile=XXX dlllist -p 1234
```

查看notepad文本：

```bash
vol -f mem.vmem --profile=XXX notepad
```

查看有关编辑控件：

```bash
vol -f mem.vmem --profile=XXX editbox
```

保存基于GDI窗口的伪截屏：

```bash
vol -f mem.vmem --profile=XXX screenshot
```

查看剪贴板：

```bash
vol -f mem.vmem --profile=XXX clipboard
```

查看IE浏览器历史记录：

```bash
vol -f mem.vmem --profile=XXX iehistory
```

查看linux_bash命令：

```bash
vol -f mem.vmem --profile=XXX linux_bash
```

分析linux系统的进程和环境：

```bash
vol -f mem.vmem --profile=XXX linux_psaux
```

查看linux的dmesg缓冲区日志消息：

```bash
vol -f mem.vmem --profile=XXX linux_dmesg
```

检查linux系统调用表是否被修改过：

```bash
vol -f mem.vmem --profile=XXX linux_check_syscall | grep HOOKED
```

检查分析内存中恶意软件特征：

```bash
vol -f mem.vmem --profile=XXX malfind
```
