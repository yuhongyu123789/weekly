---
title: Nmap基本使用
date: 2024-01-01 12:14:37
tags: 渗透测试
mathjax: true
---

# Nmap基本使用

## 常用参数

-A：进攻性扫描

-T4：4级时序，级别0·6，越高速度越快，越容易被WAF或IDS检测屏蔽，推荐T4

-v：显示冗余信息

-iL：文件中导入目标

--exclude：后面参数不在扫描范围内

--excludefile：导入的文件不在扫描范围内

--traceroute：跟踪每个路由节点

-sV：版本侦测

-sF：使用FIN秘密扫描方式协助探测TCP端口状态

-Pn：指定主机视为已开启，跳过主机发现过程

-n：不进行DNS解析

--reason：解释该IP及端口为什么开放

-sC或--script：添加脚本，不指定使用默认脚本

-O：操作系统及版本检测

-sP：基于icmp的扫描

-p-：扫描0~65535所有端口，默认只前1000

--min-rate：每秒最少发送数据包数量，值越高速度越快

-sn：仅使用ping的方法发现主机

-sT、-sU：分别进行详细的TCP、UDP端口扫描（-sU要root，还很慢...）

-oG：输出结果到文件

## 基本方法

扫描单/多个目标地址：

```bash
nmap 192.168.0.100
nmap 192.168.0.100 192.168.0.105
nmap 192.168.0.100-110
nmap 192.168.100/24 #扫描同一个C网段
nmap -p 22 192.168.41.* -oG /tmp/nmap-targethost-tcp455.txt
nmap -p 1-50 192.168.29.136 #扫描1-50端口
nmap -p 21,23 192.168.29.136
nmap -iL targets.txt #扫描targets.txt中列出的目标地址
nmap 192.168.0.100/24 -exclude 192.168.0.105 #排除某个目标地址
nmap 192.168.0.100/24 -excludefile targets.txt
nmap 192.168.0.100 -p 21,22,23,80 #扫描指定端口
nmap --traceroute 192.168.0.105 #路由跟踪
nmap -sP 192.168.41.136 #查看主机是否在线
nmap -sP 192.168.0.100/24 #目标地址所在C段在线情况
namp -O 192.168.0.105 #指纹识别目标的操作系统版本
nmap -sV 192.168.0.105 #开放端口对应的服务版本
nmap -sF -T4 192.168.0.105 #探测防火墙状态，RST端口未开放，open开放，filter有防火墙
```

## 进阶应用

默认脚本放在/nmap.scripts文件夹下。

```bash
nmap --script=auth 192.168.0.105 #鉴权扫描：应用弱口令检测
nmap --script=brute 192.168.0.105 #暴力破解：对数据库、SMB、SNMP等进行简单暴力猜解
nmap --script=vuln 192.168.0.105 #漏扫
nmap --script=realvnc-auth-bypass 192.168.0.105 #应用服务扫描，如VNC服务
nmap -n -p 445 --script=broadcast 192.168.0.105 #探测局域网内更多服务开启情况
nmap --script external baidu.com #Whois解析等其他信息查询
nmap --script broadcast-dhcp-discover #DHCP监听
```

## 骚操作

```bash
nmap -sP 192.168.123.173
nmap -p 80 --script http-iis-short-name-brute 192.168.123.173 #Windows服务器iis短文件名泄漏
nmap -sV -p 1121 --script memcached-info 192.168.123.173 #Memcached未授权访问
nmap -p 27017 --script mongodb-info 192.168.123.173 #mongodb未授权访问
nmap -p 6370 --script redis-info 192.168.123.173 #redis未授权访问
nmap --script http-slowloris --max-parallelism 400 blog.bbskali.cn #DDoS
nmap --script ftp-brute --script-args brute.emptypass=true,ftp-brute.timeout=30,userdb=/root/zi.txt,brute.useraspass=true,passdb=/root/passwords.txt,brute.threads=3,brute.delay=6 192.168.123.173 #ftp弱口令暴力破解
nmap --script mysql-empty-password 192.168.123.173 #mysql匿名访问
nmap -v -v --script ssl-cert blog.bbskali.cn #验证ssl-cert证书问题
nmap -Pn --script ssl-date blog.bbskali.cn #验证ssl证书有限期
nmap -p 23 --script telnet-brute --script-args userdb=myusers.lst,passdb=mypwds.lst --script-args telnet-brute.timeout=8s 192.168.123.173 #暴力破解telnet
nmap -sV --script unusual-port 192.168.123.173 #精确确认端口运行的服务
nmap --script vnc-info 192.168.123.173
nmap --script vnc-brute --script-args brute.guesses=6,brute.emptypass=true,userdb=/root/zi.txt,brute.useraspass=true,passdb=/root/zi.txt,brute.retries=3,brute.threads=2,brute.delay=3 192.168.123.173 #暴力破解vnc
```

## 其他

```bash
nping --echo-client "public" echo.nmap.org #挖坑待填
```
