---
title: URLFinder基础操作
date: 2024-01-26 23:05:55
tags: 渗透测试
mathjax: true
---

# URLFinder基础操作

## 用途

查找隐藏在页面或js中的敏感或未授权api接口。

## 常用参数

* -u 目标URL

* -s 显示指定状态码，all全部

* -m 默认1正常抓取 2深入抓取（url只深入一层防抓偏） 3安全深入抓取（过滤delete、remove等敏感路由）

* -f 从文件中批量读取URL抓取

* -o 结果导出csv文件

## 常见用法

```bash
URLFinder -u http://www.baidu.com -s all -m 2
URLFinder -u http://www.baidu.com -s 200,403 -m 2
URLFinder -s all -m 2 -f url.txt -o .
```
