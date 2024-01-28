---
title: Arjun入门
date: 2024-01-19 18:39:40
tags: 渗透测试
mathjax: true
---

# Arjun入门

检测可能的传参变量，例如id等：

```bash
arjun -u http://www.wangehacker.cn/sqli-labs/Less-1/
```

可指定某种方式，如GET POST JSON XML等。

```bash
arjun -u http://www.wangehacker.cn/sqli-labs/Less-1/ -m POST
```

多线程，默认2：

```bash
arjun -u http://www.wangehacker.cn/sqli-labs/Less-1/ -t 10
```

自定义标头：

```bash
arjun -u http://www.wangehacker.cn/sqli-labs/Less-1/ --headers "Accept=Language: en-US\nCookie: null"
```

控制查询块大小，可能最多达到500个参数而达到服务器最大URL长度限制，例如限制到250：

```bash
arjun -u http://www.wangehacker.cn/sqli-labs/Less-1/ -c 250
```
