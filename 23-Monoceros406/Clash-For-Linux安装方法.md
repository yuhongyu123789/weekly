---
title: Clash-For-Linux安装方法
date: 2023-12-23 10:00:05
tags: Clash
mathjax: true
---

# Clash-For-Linux安装方法

## 安装

```bash
git clone https://github.com/Elegybackup/clash-for-linux-backup.git
cd clash-for-linux-backup
vim .env
```

在`.env`文件的CLASH_URL填上自己的订阅地址。

## 启动

```bash
sudo bash start.sh
source /etc/profile.d/clash.sh
proxy_on
```

## 检查

检查服务端口，应开放9090、7890、7891、7892：

```bash
netstat -tln | grep -E '9090|789.'
```

检查环境变量，两个都应该设置：

```bash
env | grep -E 'http_proxy|https_proxy'
```

## 修改配置并重启

如果需要修改配置，修改`conf/config.yaml`，并运行：

```bash
sudo bash restart.sh
```

## 停止

```bash
sudo bash shutdown.sh
proxy_off
```

## 访问Clash Dashboard

浏览器访问：http://(local-ip):9090/ui，`start.sh`运行后会显示。

API Base URL输入http://(local-ip):9090，Secret输入`start.sh`显示的Secret。Add后可进入。

查询本机IP地址：

```bash
ifconfig
```
