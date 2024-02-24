---
title: Recon-ng初探
date: 2024-01-13 20:29:23
tags: 渗透测试
mathjax: true
---

# Recon-ng初探

## 模块安装

Kali自带Recon-ng，但模块一个没装，得科学上网，然后recon-ng的shell中执行：

```bash
marketplace install all
```

## 示例

选择模块：

```bash
modules load recon/domains-hosts/hackertarget
```

查看模块信息：

```bash
info
```

设置目标：

```bash
options set SOURCE blog.bbskali.cn
```

运行模块：

```bash
show hosts
```

## 实战

枚举百度网站子域名，并导出为csv。

```
help
show modules
use recon/domains-hosts/baidu_site
show options
set SOURCE baidu.com
run
use reporting/csv
run
```
