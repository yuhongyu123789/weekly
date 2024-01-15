---
title: PDF加密解密
date: 2024-01-13 20:29:15
tags: PDF
mathjax: true
---

# PDF加密解密

给文件添加密码：

```bash
pdftk kali.pdf output out.pdf user_pw 666666 #kali.pdf加密为out.pdf 密码为666666
```

暴力破解：

```bash
pdfcrack -f out.pdf -n 6 -m 8 -c 0123456789 #-f要破解的文件 -n最短多少字符 -m 最长多少字符 -c使用的字符集
```

支持字典：

```bash
pdfcrack -f out.pdf -w pass.txt
```
