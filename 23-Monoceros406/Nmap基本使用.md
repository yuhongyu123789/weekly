---
title: Nmap基本使用
date: 2024-01-01 12:14:37
tags: Nmap
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

## 基本方法

扫描单/多个目标地址：

```bash
nmap 192.168.0.100
nmap 192.168.0.100 192.168.0.105
nmap 192.168.0.100-110
nmap 192.168.100/24 #扫描同一个C网段
nmap -p 1-50 192.168.29.136 #扫描1-50端口
nmap -p 21,23 192.168.29.136
nmap -iL targets.txt #扫描targets.txt中列出的目标地址
nmap 192.168.0.100/24 -exclude 192.168.0.105 #排除某个目标地址
nmap 192.168.0.100/24 -excludefile targets.txt
nmap 192.168.0.100 -p 21,22,23,80 #扫描指定端口
nmap --traceroute 192.168.0.105 #路由跟踪
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
