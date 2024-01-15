---
title: John-The-Ripper入门
date: 2023-12-31 11:11:33
tags: John
mathjax: true
---

# John-The-Ripper入门

## 哈希破解

自动破解：

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

如果不能很好的自动识别哈希类型，需要hash-identifier识别，并使用指定语法破解：

```bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hash_to_crack.txt
```

查看所有支持的格式：

```bash
john --list=formats
```

## 破解Windows身份验证

```bash
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt ntlm.txt
```

## 破解shadow

其中local_passwd和local_shadow分别为/etc/passwd和/etc/shadow的副本：

```bash
unshadow loca_passwd local_shadow > unshadowed.txt
```

提供给john破解：

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=sha512crypt unshadowed.txt
```

## 单哈希破解模式

将哈希改为john的文件格式，例如更改文件hashes.txt：

```
mike:1efee03cdcb96d90ad48ccc7b8666033
```

然后丢给john：

```bash
john --single --format=xxx hashes.txt
```

## 自定义规则

略。

## zip、rar、ssh密码破解

```bash
zip2john zipfile.zip > zip_hash.txt
john --wordlist=/usr/share/wordlist/rockyou.txt zip_hash.txt

rar2john rarfile.rar > rar_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt rar_hash.txt

ssh2john id_rsa > id_rsa_hash.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa_hash.txt
```



