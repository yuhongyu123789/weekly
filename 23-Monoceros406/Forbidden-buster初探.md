---
title: Forbidden_buster初探
date: 2024-01-26 22:28:56
tags: Forbidden Buster
mathjax: true
---

# Forbidden_buster初探

## 基本方法

利用各种技术方法和Header测试绕过401、403访问控制限制。

```bash
forbidden_buster -u http://example.com
```

## 常用参数

* -u | --url 待测Web应用程序完整URL路径

* -m | --method 测试方法 默认GET

* -H | --header 添加一个自定义Header

* -d | --data 向请求体中添加数据 支持JSON

* -p | --proxy 使用代理

* --include-api API模糊测试

* --include-unicode Unicode模糊测试 速度慢

* --include-user-agent User-Agent模糊测试 速度慢

## 进阶方法

```bash
forbidden_buster --url "https://example.com/api/v1/secret" --method POST --header "Authorization: Bearer XXX" --data '{\"key\":\"value\"}' --proxy "http://proxy.example.com" --include-api --include-unicode
```
