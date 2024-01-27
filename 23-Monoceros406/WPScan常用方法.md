---
title: WPScan常用方法
date: 2024-01-19 18:45:07
tags: 渗透测试
mathjax: true
---

# WPScan常用方法

## 参数

* --url|-u <target url> 扫描指定URL或域名。

* --force|-f 如果正在WordPress强制WPScan不检查。

* --enumerate|-e [option(s)] 信息枚举

    * u 用户名id 1-10

    * u[10-20] 指定用户名id 10-20

    * p 插件程序

    * vp 仅漏洞插件程序

    * ap 所有插件程序（慢）

    * tt timthumbs

    * t 主题

    * vt 仅漏洞主题

    * at 所有主题（慢）

## 常见用法

```bash
wpscan -u 192.168.41.130 #收集基本信息
wpscan -u 192.168.41.130 -e u vp #列出用户名列表、漏洞插件
wpscan -u 192.168.41.130 -e u --wordlist /root/wordlist.txt #用户名密码爆破
```
