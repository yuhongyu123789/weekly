---
title: Wfuzz基础操作
date: 2024-01-19 18:46:13
tags: 渗透测试
mathjax: true
---

# Wfuzz基础操作

爆后台文件：

```bash
wfuzz -w wordlist URL/FUZZ.php
```

其中wordlist填字典目录，URL即为具体连接，关键字FUZZ为爆破点。

爆后台目录：

```bash
wfuzz -w wordlist URL/FUZZ
```

爆URL参数：

```bash
wfuzz -z range,000-99 url/xxx.php?id=FUZZ
```

其中range为id的取值范围。

指定cookie或session：

```bash
wfuzz -z range,000-999 -b session=session -b cookie=cookie url/xxx.php?id=FUZZ
```

递归测试，在已找出的目录再递归一次：

```bash
wfuzz -z list,"admin-login.php-test.php" -R 1 URL/FUZZ
```

POST双传参：

```bash
wfuzz -w /usr/share/wfuzz/wordlist/general/mima.txt -w /usr/share/wfuzz/wordlist/general/mima.txt -d "username=FUZZ&password=FUZZ" http://192.168.46.129/vulnerabilities/brute
```

自定义headers爆破：

```bash
wfuzz -w word.txt -H "user-agent:aaa" URL/FUZZ
```

设置代理，可多个-p选项：

```bash
wfuzz -w word.txt -p localhost:8000 url
wfuzz -w word.txt -p localhost:8000: SOCKS4 url
```

并发控制，不指定默认10：

```bash
wfuzz -w wordlist -t 5 URL/FUZZ
```

指定将结果输出为文件的格式，支持csv html json magictree raw：

```bash
wfuzz -f outfile.html -w wordlist URL/FUZZ
```
