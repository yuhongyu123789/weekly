---
title: Kali-Linux渗透测试入门
date: 2024-01-13 20:28:45
tags: 渗透测试
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
dmitry -w baidu.com #whois
dmitry -s baidu.com -o subdomain #-s查子域名 -o输出文件 要挂梯子
dmitry -p 192.168.29.136 #端口扫描
```

#### dnsenum

收集域名信息，如：主机地址信息、域名服务器、邮件交换记录等。

```bash
dnsenum --enum benet.com
dnsenum -w baidu.com #WHOIS请求
    #--threads [number] 多进程
    #-r 递归查询
    #-d WHOIS请求之间时间延迟数 s
    #-o 指定输出位置
```

#### fierce

跟dnsenum差不多。

```bash
fierce -dns baidu.com
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

#### snmpwalk

查询指定的所有SNMP对象标识OID树信息，例如测试Windows主机：

```bash
snmpwalk -c public 192.168.41.138 -v 2c
```

枚举安装的软件：

```bash
snmpwalk -c public 192.168.41.138 -v 1
```

枚举打开的TCP端口：

```bash
snmpwalk -c public 192.168.41.138 -v 1 | grep tcpConnState | cut -d "." -f6 | sort -nu
```

#### snmpcheck

好像改版了？？？先不写了。

#### scapy

多行并行跟踪路由。

```python
ans,unans=sr(IP(dst="www.rzchina.net/30",ttl=(1,6))/TCP()) #建立连接
ans.make_table(lambda(s,r):(s.dst,s.ttl,r.src)) #查看数据包发送情况
res,unans=traceroute(["www.google.com","www.kali.org","www.rzchina.net"],dport=[80,443],maxttl=20,retry=-2) #查看TCP路由跟踪信息
res.graph() #以图的形式显示 但是报错？？
res.graph(target=">/tmp/graph.svg") #保存
exit()
```

### 扫描端口

略。

### 识别操作系统

#### TTL识别

| 操作系统                                                                                 | TTL值 |
| ------------------------------------------------------------------------------------ | ---- |
| UNIX                                                                                 | 255  |
| Compaq Tru64 5.0                                                                     | 64   |
| Windows XP-32bit                                                                     | 128  |
| Linux Kernel 2.2.x & 2.4.x                                                           | 64   |
| FreeBSD 4.1,4.0,3.4、Sun Solaris 2.5.1,2.6,2.7,2.8、OpenBSD 2.6,2.7/NetBSD、HP UX 10.20 | 255  |
| Windows 95/98/98SE、Windows ME                                                        | 32   |
| Windows NT 4 WRKS、Windows NT4 Server、Windows2000、Windows XP/7/8/10                   | 128  |

#### amap

amapcrap将随机数据发送到UDP、TCP或SSL端口，获取非法相应信息，识别服务信息。获取到的信息写入appdefs.trig和appdefs.resp文件，便于amap下一步检测。

```bash
amapcrap -n 20 -m a 192.168.29.137 80 -v #探测80端口应用程序 -n最大连接数 -m发送的伪随机数：0空字节；a字母空格；b二进制 -v详细
```

amap尝试识别运行在非正常端口上的应用程序。

```bash
amap -bqv 192.168.29.137 80 #-b显示接受的服务标识信息 -q不显示关闭端口 -v详细信息
amap -bq 192.168.29.137 50-100
```

#### p0f

被动指纹识别，分析Wireshark捕获的流量包，信息包含：操作系统、端口、防火墙、NAT、负载均衡、已启动时间、DSL/ISP信息等。

```bash
p0f -r /tmp/targethost.pcap -o p0f-result.log
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
