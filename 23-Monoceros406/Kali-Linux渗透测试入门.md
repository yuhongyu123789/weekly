---
title: Kali-Linux渗透测试入门
date: 2024-01-13 20:28:45
tags: Kali
mathjax: true
---

# Kali-Linux渗透测试入门

## 信息收集

### 发现主机

#### netmask

将IP范围转换为CIDR格式：

```bash
netmask -c 192.168.0.0:192.168.2.255
```

将IP范围转换为标准子网掩码格式：

```bash
netmask -s 192.168.0.0:192.168.2.0
```

#### traceroute

获取目标主机路由条目：

```bash
traceroute 62.234.110.28
```

#### nmap

略。

#### netdiscover

检查在线主机。

```bash
netdiscover #尽可能发现多个在线主机
netdiscover -r 192.168.1.0/24 #指定区间
netdiscover -p #被动ARP监听
```

### 域名分析

#### whois

查找指定帐号/域名的用户相关信息。

```bash
whois baidu.com
```

#### dmitry

用途同上。

```bash
dmitry -w baidu.com
dmitry -s baidu.com -o subdomain #-s查子域名 -o输出文件 要挂梯子
dmitry -p 192.168.29.136 #端口扫描
```

#### dnsenum

收集域名信息，如：主机地址信息、域名服务器、邮件交换记录等。

```bash
dnsenum -w baidu.com
```

#### nslookup

查询DNS记录，验证域名解析是否正常。

```bash
nslookup www.baidu.com
nslookup #进入交互模式
set type=ns #模式为查看NS记录
baidu.com
exit
```

#### ping

检查网络是否联通。

```bash
ping -c 4 www.baidu.com #发送4次
```

### 扫描端口

略。

### 识别操作系统

#### TTL识别

| 操作系统                                                     | TTL值 |
| ------------------------------------------------------------ | ----- |
| UNIX                                                         | 255   |
| Compaq Tru64 5.0                                             | 64    |
| Windows XP-32bit                                             | 128   |
| Linux Kernel 2.2.x & 2.4.x                                   | 64    |
| FreeBSD 4.1,4.0,3.4、Sun Solaris 2.5.1,2.6,2.7,2.8、OpenBSD 2.6,2.7/NetBSD、HP UX 10.20 | 255   |
| Windows 95/98/98SE、Windows ME                               | 32    |
| Windows NT 4 WRKS、Windows NT4 Server、Windows2000、Windows XP/7/8/10 | 128   |

#### amap

amapcrap将随机数据发送到UDP、TCP或SSL端口，获取非法相应信息，识别服务信息。获取到的信息写入appdefs.trig和appdefs.resp文件，便于amap下一步检测。

```bash
amapcrap -n 20 -m a 192.168.29.137 80 -v #探测80端口应用程序 -n最大连接数 -m发送的伪随机数：0空字节；a字母空格；b二进制 -v详细
```

amap尝试识别运行在非正常端口上的应用程序。

```bash
amap -bqv 192.168.29.137 80 #-b显示接受的服务标识信息 -q不显示关闭端口 -v详细信息
```

### 服务信息

#### smbclient

SMB服务客户端。

```bash
smbclient -L 192.168.19.130 -U root
```

#### snmp-check

枚举SNMP设备，获取目标主机信息。

获取信息有：系统信息、用户账户、网络信息、网络接口、网络IP、路由信息、监听的TCP/UDP、网络服务、进程信息、存储信息、文件系统信息、设备信息、软件组件。

```bash
snmp-check 192.168.1.101
```

### 信息分析整理

#### Maltego

略。

## 漏洞扫描

### 基本漏洞

#### changeme

自动检查各种服务弱密码：

```bash
changeme -a 192.168.29.132 #-a扫描所有协议
```

### Nessus

略。

### GVM/OpenVAS

略。

### 其他方式

#### unix-privesc-check

检查所在系统的提权漏洞。

```bash
unix-privesc-check standard #快
unix-privesc-check detailed #慢 可找出微小漏洞 会误报
```

## 漏洞利用

### Metasploit

略。
