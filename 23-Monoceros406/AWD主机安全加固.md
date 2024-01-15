---
title: AWD主机安全加固
date: 2023-12-22 18:55:59
tags: AWD
mathjax: true
---

# AWD主机安全加固

## 用户权限排查

```bash
awk -F : '($3>=500 || $3==0){print $1}' /etc/passwd #查询UID=0和UID>=500的用户名
awk -F : '($3==0){print $1}' /etc/passwd
sudo awk -F ":" '($2--""){print $1}' /etc/shadow #查询密码为空的用户
```

Hydra检测SSH服务器弱口令：

```bash
hydra -l test -P /opt/passwd.txt -V -t 5 ssh://192.168.5.160
```

禁用特权用户：

```bash
sudo passwd -l xxx #禁用xxx用户
sudo passwd -u xxx #解禁xxx用户
sudo userdel xxx #删除xxx用户
sudo useradd xxx #添加xxx用户
sudo passwd xxx #更改xxx用户的密码
```

## 远程连接配置

默认SSH端口为22，修改`/etc/ssh/sshd_config`配置中“Port”一行，改变端口：

```
Port 20022
```

重启SSH服务，并使用设定的端口连接：

```bash
ssh beta@192.168.5.160 -p 20022
```

使用Nmap可探测目标开启SSH服务的端口号：

```bash
sudo nmap -sS -Pn -A -p20022 192.168.5.160
```

为隐藏SSH服务，在`sshd_config`中增加一行：

```
DebianBanner no
```

并清除SSH指纹携带的版本信息：

```bash
sudo sed -i 's/OpenSSH_8.2p1/welcome_0.0p0/g' /usr/sbin/sshd
```

禁止特权用户远程登录，将`sshd_config`中改为：

```
PermitRootLogin no
```

防SSH弱口令枚举攻击，在`/etc/pam.d/login`中添加以下内容：

```
auth required pam_tally2.so onerr=fail deny=1 unlock_time=30 even_deny_root root_unlock_time=30
```

参数说明：

| 参数             | 意义                                         |
| ---------------- | -------------------------------------------- |
| even_deny_root   | 限制root用户                                 |
| deny             | 设置普通用户和root用户连续错误登录的最大次数 |
| unlock_time      | 普通用户锁定后多长时间解锁，单位s            |
| root_unlock_time | 同上，root                                   |

并在`/etc/pam.d/sshd`文件中添加：

```
auth required pam_tally2.so onerr=fail deny=1 unlock_time=30 even_deny_root root_unlock_time=30
account required pam_tally2.so
```

系统中执行以下命令查看远程连接输错密码的次数：

```bash
sudo pam_tally2 --user
```

## SUID/SGID文件权限排查

查询具有SUID权限的文件：

```bash
find / -per -u=s -type f 2?/dev/null
```

例如`find`有SUID权限，通过`find`使普通用户获得root：

```bash
/usr/bin/find -name 123.ico -exec whoami \;
```

当Polkit的pkexec<=0.120具有SUID权限时，使用CVE-2021-4034提权。修改SUID方法如下：

```bash
chmod u-s /usr/bin/find
find / -perm -u=s -type f 2>/dev/null
touch test
/usr/bin/find -name test -exec whoami \;
```

## 不安全服务排查

文件共享服务端口：

| 端口     | 说明                 | 安全问题                           |
| -------- | -------------------- | ---------------------------------- |
| 21/22/69 | FTP/TFTP文件传输协议 | 允许匿名上传、下载、破解和嗅探攻击 |
| 2049     | NFS服务              | 配置不当                           |
| 139      | Samba服务            | 破解、未授权访问、远程代码执行     |
| 389      | LDAP（目录访问协议） | 注入、允许匿名访问、弱口令         |

远程连接服务端口：

| 端口 | 说明            | 安全问题                                  |
| ---- | --------------- | ----------------------------------------- |
| 22   | SSH远程连接     | 破解、SSH隧道及内网代理转发、文件传输     |
| 23   | Telnet远程连接  | 破解、嗅探、弱口令                        |
| 3389 | RDP远程桌面连接 | Shift后门（Windows Server2003以下）、破解 |
| 5900 | VNC             | 弱口令破解                                |
| 5632 | PyAnywhere服务  | 抓密码、代码执行                          |

Web应用服务端口：

| 端口        | 说明                      | 安全问题                      |
| ----------- | ------------------------- | ----------------------------- |
| 80/443/8080 | 常用Web服务端口           | Web攻击、破解、服务器版本漏洞 |
| 7001/7002   | WebLogic控制台            | Java反序列化、弱口令          |
| 8080/8089   | Jboss/Resin/Jetty/JenKins | 反序列化、控制台弱口令        |
| 9090        | WebSphere控制台           | Java反序列化、弱口令          |
| 4848        | GlassFish控制台           | 弱口令                        |
| 1352        | Lotus domino邮件服务      | 弱口令、信息泄露、破解        |
| 10000       | Webmin-Web控制面板        | 弱口令                        |

数据库服务端口：

| 端口        | 说明             | 安全问题                   |
| ----------- | ---------------- | -------------------------- |
| 3306        | MySQL            | 注入、提权、破解           |
| 1433        | MSSQL            | 注入、提权、SA弱口令、破解 |
| 1521        | Oracle数据库     | TNS破解、注入、反弹shell   |
| 5432        | PostgreSQL数据库 | 破解、注入、弱口令         |
| 27017/27018 | MongoDB          | 破解、未授权访问           |
| 6379        | Redis数据库      | 未授权访问、弱口令破解     |
| 5000        | SysBase/DB2      | 破解、注入                 |

邮件服务端口：

| 端口 | 说明         | 安全问题   |
| ---- | ------------ | ---------- |
| 25   | SMTP邮件服务 | 邮件伪造   |
| 110  | POP3协议     | 破解、嗅探 |
| 143  | IMAP协议     | 破解       |

网络常见协议端口：

| 端口  | 说明        | 安全问题                              |
| ----- | ----------- | ------------------------------------- |
| 53    | DNS域名系统 | 允许区域传送、DNS劫持、缓存投毒、欺骗 |
| 67/68 | DHCP服务    | 劫持、欺骗                            |
| 161   | SNMP协议    | 破解、搜集目标内网信息                |

特殊服务端口：

| 端口        | 说明                   | 安全问题            |
| ----------- | ---------------------- | ------------------- |
| 2181        | Zookeeper服务          | 未授权访问          |
| 8069        | Zabbix服务             | 远程执行、SQL注入   |
| 9200/9300   | Elasticsearch          | 远程执行            |
| 11211       | Memcache服务           | 未授权访问          |
| 512/513/514 | Linux Rexec服务        | 破解、Rlogin登录    |
| 873         | Rsync服务              | 匿名访问、文件上传  |
| 3690        | Svn服务                | Svn泄露、未授权访问 |
| 50000       | SAP Management Console | 远程执行            |

查询系统服务的运行级信息：

```bash
chkconfig --list
```

查询系统服务运行情况：

```bash
systemctl list-units --type=service
```

关闭服务的两种方法：

```bash 
sudo systemctl stop mysql
sudo service mysql stop
```

查询任何用户都有写权限的文件夹：

```bash
find / -xdev -mount -type d \( -perm -0002 -a | -perm -1000 \)
```

查询任何用户都有写权限的文件：

```bash
for PART in `grep -v ^# /etc/fstab | awk '($6!="0"){print $2}'`; do
	find $PART -xdev -type f \( -perm -0002 -a | -perm -1000 \) -print
done
```

查询系统隐藏文件：

```bash
find / -name "." -print -xdev
```

查询bash历史记录，清除：

```bash
history
history -c
```

修改系统允许记录的历史命令行数为3：

```bash
echo $HISTSIZE
$HISTSIZE=3
source /etc/profile
```

## 系统日志安全配置

`\var\log`下默认日志文件：

| 日志目录         | 功能描述                               |
| ---------------- | -------------------------------------- |
| messages         | 内核消息级各种应用程序的公共日志信息   |
| cron             | crond计划任务产生的事件信息            |
| dmesg            | 操作系统在引导过程中的各种事件信息     |
| mailog           | 进入或发出系统的电子邮件活动           |
| lastlog          | 每个用户最近的登录事件                 |
| secure或auth.log | 用户认证相关的安全事件信息             |
| wtmp             | 每个用户登录、注销、系统启动、停机事件 |
| btmp             | 失败的、错误的登录尝试及验证事件       |
| boot.log         | 启动有关的日志文件                     |

查看用户登录系统记录的信息：

```bash
last -f /var/log/wtmp
```

系统日志备份略。

## Apache中间件安全加固

配置文件目录，Ubuntu和Debian在`/etc/apache2/apache2.conf`，CentOS在`/etc/httpd/conf/httpd.conf`。

修复目录遍历：将配置文件中“Options Indexes FollowSymLinks”改为“Options FollowSymLinks”。

修复版本信息泄露：在配置文件最后添加：

```
ServerSignature Off
ServerTokens Prod
```

或也可以重定向到404页面：

```
ErrorDocument 404 /404.html
```

当开启WebDAV模块时，支持多种扩展请求方式，攻击者利用扩展请求方式进行恶意操作，例如：

利用OPTIONS请求获取目标网站支持的请求方式：

```bash
curl -v -X OPTIONS http://192.168.5.160/
```

利用PUT请求对网站进行木马上传：

```bash
curl -v -X PUT -T "test.txt" http://192.168.5.160/
```

为禁用WebDAV模块，修改配置文件为以下，或直接删除该条语句。

```
Dav Off
```

修复多文件后缀解析漏洞（例如`info.php.png`），配置文件加入以下条件语句：

```
<Files ~ "\.(php.)">
	Order Allow,Deny
	Deny from all
</Files>
```

## Nginx中间件安全加固

配文件目录，Ubuntu和Debian在`/etc/nginx/nginx.conf`，CentOS在`/usr/local/nginx/conf/nginx.conf`。

修复目录遍历：将配置文件中“autoindex on”改为“autoindex off”。

修复版本信息泄露：将配置文件中“server_tokens on”修改为“server_tokens off”。

多文件后缀解析漏洞（例如文件路径`info.jpg/x.php`），原理如下：

当PHP遇到文件路径`info.jpg/x.php`时，如果`php.ini`中将`cgi.fix_pathinfo`设置为1，用于修复路径，如果不存在则采用上层路径（即去掉`/x.php`），继续判断`info.jpg`是否存在。如果`fpm/pool.d/www.conf`中`security.limit_extensions`配置为空时，导致允许fastcgi将png、jpg等文件当作PHP代码解析。

例如：上传`info.jpg`，访问`127.0.0.1/info.jpg/x.php`即可。

修复方法：在`php.ini`中注释“cgi.fix_pathinfo=0”，在`fpm/pool.d/www.conf`中`security.limit_extensions`后添加“.php”。

## Tomcat中间件安全加固

常见攻击手段：通过BurpSuite抓包猜解Web应用程序管理平台弱口令，成功后在JSP木马所在目录执行：

```bash
jar -cvf shell.war *
```

生成含木马的.war包，并在Web应用程序管理后台上部署，通过蚁剑连接。

避免Tomcat Manager远程弱口令猜解攻击，修改`conf/tomcat-users.xml`中`password`字段：

```xml
<user username="amdin" password="admin" roles="tomcat,manager-gui,amdin-gui,admin-script,manager-script"/>
```

禁止远程主机访问请求，只允许本地正常访问。修改`/webapps/manager/META-INF/context.xml`：

```xml
<Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1"/>
```

修复目录遍历，修改`conf/web.xml`，将`listing`由false改为true。

常见攻击手段：构造“1.jsp/”或“1.jsp%20”，通过PUT请求上传JSP木马，蚁剑连接。

修复方法：修改`web.xml`的`readonly`参数，由false改为true。

日志存储在`/tomcat/logs`目录下。

## PHP安全加固

修复远程文件包含：修改`php.ini`，将`allow_url_include`的值由on改为off。

危险函数：system、exec、shell_exec、scandir、chgrp、proc_open等。

修复命令执行或文件读取：将危险函数加入`php.ini`的`disable_functions`函数中：

```ini
disable_functions=system,exec,...
```

限制PHP只允许读取`/var/www/html`：修改`php.ini`为：

```ini
open_basedir=/var/www/html
```

常见攻击手段：构造语法错误的请求，回应中报头“X-Powered-By”泄露PHP版本和系统版本。

修复方法：修改`php.ini`中`expose_php`为Off，修改`display_errors`为Off。

## MySQL数据库安全加固

常见攻击手段：Hydra对数据库进行弱口令暴力破解攻击：

```bash
sudo hydra -l root -P password.txt mysql://192.168.5.160 -I
```

设置只允许本地连接，不允许远程主机连接：

```sql
use mysql;
select host,user from user;
update user set host="localhost" where user='root';
select host,user from user;
```

修改`mysql/mysql.conf.d/mysqld.cnf`或`mysql/my.cnf`中`bind-address`字段和`mysqlx-bind-address`的值为“127.0.0.1”，使远程主机探测不到MySQL开放的TCP端口。

修改root登录口令：

```sql
-- MySQL 8
use mysql;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'AnyWhereis5@0';

--MySQL 5
use mysql;
update user set authentication_string=password("AnyWhereis5@0") where user='root';
```

常见攻击手段：用以下语句查询：

```sql
show variables like '%secure%';
```

当`secure_file_priv`的值为空时，目标MySQL具备写入文件的功能，写入一句话木马：

```sql
select '<?php @eval($_POST[passwd]);?>' into outfile '/var/www/html/shell.php';
```

即使设置了`secure_file_priv`参数，也可通过日志文件写入。将`generral_log_file`变量的值设为“/var/www/html/shell.php”，然后执行：

```sql
show variables like '%general%';
set global general_log=on;
select '<?php @eval($_POST[passwd]);?>';
set global general_log=off;
```

修复方法：将`secure_file_priv`参数设置为“NULL”，禁用root远程连接，并使用降权后普通用户权限进行连接：

```sql
revoke ALL on *.* from admin@'%';
```

开启并查看日志路径：

```sql
set global general_log=on;
show variables like '%general%';
```

## Redis数据库安全加固

常用攻击手段：在Kali中，使用以下命令连接：

```bash
redis-cli -h IP地址
```

常用Redis指令：

| 指令         | 说明                             |
| ------------ | -------------------------------- |
| info         | 查看版本号、配置文件目录、进程ID |
| keys *       | 查看key及其对应值                |
| get user     | 获取用户名                       |
| get password | 获取登陆指令                     |
| flushall     | 删除所有数据                     |
| dir          | 生成rdb的文件路径                |
| save         | 保存配置                         |

写入一句话木马

```
config set dir /var/www/html/
config set dbfilename shell.php
set WebShell "<?php @eval($_POST[passwd]);?>"
save
```

修改`/etc/redis/redis.conf`，设置`requirepass`值，利用redis-cli攻击时，需要命令“auth 密码”。设置`bind`为“127.0.0.1::1”，则不能远程连接。
