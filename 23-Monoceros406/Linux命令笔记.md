---
title: Linux命令笔记
date: 2023-10-25 09:01:15
tags: Shell
mathjax: true
---

# Linux命令笔记

## 基础命令

### 时间日期

```bash
date
```

### 月份日历

```bash
cal
```

### 磁盘可用空间

```bash
df
```

### 可用内存容量

```bash
free
```

### 结束终端会话

```bash
exit
```

## 导航&探索

### 当前工作目录

```bash
pwd
```

### 改变当前目录

```bash
ch - #切换回前一个工作目录
ch ~user_name #更换为用户user_name主目录
```

### ls

```bash
ls
ls /usr
ls ~ /usr
```

| 选项                | 描述                                           |
| ------------------- | ---------------------------------------------- |
| -a,--all            | 列出所有文件。                                 |
| -A,--almost-all     | 同上 不列出.和..。                             |
| -d,--directory      | 与-l结合使用，查看目录详细信息，而非其中内容。 |
| -F,--classify       | 名称后加类型指示符。                           |
| -h,--human-readable | 人类可读形式显示文件大小。                     |
| -l                  | 长格式输出。                                   |
| -r,--reverse        | 降序，一般字母升序。                           |
| -S                  | 按照大小排序。                                 |
| -t                  | 按照修改日期排序。                             |

### file

确定文件类型。

```bash
file filename
```

### less

查看文本文件。

```bash
less filename
```

| 命令             | 操作                 |
| ---------------- | -------------------- |
| Page Up或b       | 后翻一页。           |
| Page Down或Space | 前翻一页。           |
| 上方向键         | 向后一行。           |
| 下方向键         | 向前一行。           |
| G                | 移动到文本文件末尾。 |
| IG或g            | 移动到文本文件开头。 |
| /characters      | 向前搜索指定字符串。 |
| n                | 重复上一次搜索。     |
| h                | 显示帮助信息。       |
| q                | 退出less。           |

### 系统目录

| 目录           | 注释                                                         |
| -------------- | ------------------------------------------------------------ |
| /              | 根目录。                                                     |
| /bin           | 系统引导执行的二进制可执行文件。                             |
| /boot          | Linux内核、初始化RAM磁盘映像、引导装载器。                   |
| /dev           | 包含设备节点的特殊目录。                                     |
| /etc           | 包含系统范围的所有配置文件。例如：crontab何时执行自动化作业；fstab存储设备及其关联的挂载点；passwd所有用户信息。 |
| /home          | 每个用户都有各自的目录。                                     |
| /lib           | 系统核心程序用到的共享库文件。类似DLL。                      |
| /lost+found    | 格式化过的分区或设备包含该目录，用于文件系统损坏时的部分恢复。一般都是空的。 |
| /media         | 各种可移动存储设备的挂载点。                                 |
| /mnt           | 同上，手动挂载。                                             |
| /opt           | 存放安装的商业软件。                                         |
| /proc          | 内核的窥探孔，可使用户了解到内核如何管理计算机。文件全部可读。并非真实文件系统，内核维护的虚拟文件系统。 |
| /root          | 超级用户主目录。                                             |
| /sbin          | 系统二进制可执行文件。                                       |
| /tmp           | 各种程序生成的临时文件。                                     |
| /usr           | 普通用户用到的所有程序和支持文件。                           |
| /usr/bin       | Linux发行版安装的程序。                                      |
| /usr/lib       | /usr/bin中程序用到的共享库。                                 |
| /usr/local     | 计划在系统范围内使用的程序。                                 |
| /usr/sbin      | 系统管理工具。                                               |
| /usr/share     | /usr/bin中程序用到的共享数据（配置文件、图标、桌面背景、声音文件）。 |
| /usr/share/doc | 大部分软件包自带文档。                                       |
| /var           | 可能会改变的数据（数据库、假脱机文件、用户邮件）。           |
| /var/log       | 日志文件、系统活动记录。比较有用的：/var/log/messages和/var/log/syslog。 |

## 操作文件和目录

### 通配符

| 通配符        | 含义                                        |
| ------------- | ------------------------------------------- |
| *             | 匹配任意多个字符。                          |
| ?             | 匹配任意单个字符。                          |
| [characters]  | 匹配属于字符集合character中任意单个字符。   |
| [!characters] | 匹配不属于字符集合character中任意单个字符。 |
| [[:class:]]   | 匹配属于字符类class中任意单个字符。         |

> 匹配点号开始的文件（隐藏文件）使用：
>
> ```bash
> ls -d .[!.]*
> ```

### 字符类

| 字符类    | 含义                       |
| --------- | -------------------------- |
| [:alnum:] | 匹配任意单个字母数字字符。 |
| [:alpha:] | 匹配任意单个字母。         |
| [:digit:] | 匹配任意单个数字。         |
| [:lower:] | 匹配任意单个小写字母。     |
| [:upper:] | 匹配任意单个大写字母。     |

### mkdir

```bash
mkdir directory...
```

> ‘...’表示可重复出现该参数。

### cp

单个文件或目录item1复制到文件或目录item2：

```bash
cp item1 item2
```

多个文件或目录item复制到目录directory中：

```bash
cp item... directory
```

| 选项             | 含义                                 |
| ---------------- | ------------------------------------ |
| -a,--archive     | 复制包括所有权与权限在内的所有属性。 |
| -i,--interactive | 覆盖文件之前提示用户确认。           |
| -r,--recursive   | 递归复制目录及其内容。               |
| -u,--update      | 只复制目标目录中不存在或更新的文件。 |
| -v,--verbose     | 显示相关信息。                       |

### mv

```bash
mv item1 item2
mv item... directory
```

| 选项             | 含义 |
| ---------------- | ---- |
| -i.--interactive |      |
| -u,--update      |      |
| -v,--verbose     |      |

### rm

```bash
rm item...
```

| 选项             | 含义                             |
| ---------------- | -------------------------------- |
| -i,--interactive |                                  |
| -r,--recursive   |                                  |
| -f,--force       | 忽略不存在的文件，不提示，屏蔽-i |
| -v,--verbose     |                                  |

### ln

创建硬链接：

```bash
ln file link
```

创建符号链接：

```bash
ln -s item link
```

## 命令

### type

显示指定的命令属于哪种类型。

```bash
type command
```

### which

显示可执行文件的位置。

```bash
which ls
```

### help

获取内建命令帮助信息。

```bash
help command
```

### man

显示命令手册页。

```bash
man program
```

搜索手册页内容：

```bash
man section search_term
```

例如：

```bash
man 5 passwd
```

| 节   | 内容                               |
| ---- | ---------------------------------- |
| 1    | 用户命令。                         |
| 2    | 系统调用的编程接口。               |
| 3    | C库函数的编程接口。                |
| 4    | 特殊文件，例如设备节点和驱动程序。 |
| 5    | 文件格式。                         |
| 6    | 游戏和娱乐，例如屏幕保护程序。     |
| 7    | 杂项。                             |
| 8    | 系统管理命令。                     |

### apropos

显示适合的命令清单。同`man -k`。

```bash
apropos command
```

### whatis

显示手册页的单行简述。

```bash
whatis command
```

### info

手册页包含超链接。

```bash
info [command]
```

| 命令               | 操作                             |
| ------------------ | -------------------------------- |
| ?                  | 显示命令帮助。                   |
| Page Up或BackSpace | 显示上一页。                     |
| Page Down或Space   | 显示下一页。                     |
| n                  | 显示下一个节点。                 |
| p                  | 显示上一个节点。                 |
| u                  | 显示当前节点的父节点，通常菜单。 |
| Enter              | 进入光标所在的超链接。           |
| q                  | 退出。                           |

### alias

创建自定义命令。

```bash
alias foo='cd /usr; ls; cd -'
foo
unalias foo
alias #显示所有别名
```

## 重定向

### 标准输入、输出、错误重定向

#### 标准输出重定向

```bash
ls -l /bin/usr > ls-output.txt
```

追加到文件尾部：

```bash
ls -l /bin/usr >> ls-output.txt
```

#### 标准错误重定向

```bash
ls -l /bin/usr 2> ls-output.txt
```

标准输出和标准错误重定向到同一个文件中：

```bash
ls -l /bin/usr > ls-output.txt 2>&1
ls -l /bin/usr &> ls-output.txt
ls -l /bin/usr &>> ls-output.txt #追加
```

#### 位桶

```bash
ls -l /bin/usr 2> /dev/null
```

#### cat

拼接文件。

```bash
cat filename...
```

例如：

```bash
cat movie.mpeg.0* > movie.mpeg
```

#### 标准输入重定向

```bash
cat < lazy_dog.txt
```

### 管道

#### sort

```bash
ls /bin /usr/bin | sort | less
```

#### uniq

重复行：

```bash
ls /bin /usr/bin | sort | uniq | less #删除重复行
ls /bin /usr/bin | sort | uniq -d | less #报告重复行
```

#### wc

统计文件中行数、单词数、字符数。

```bash 
ls /bin /usr/bin | sort | uniq | wc
ls /bin /usr/bin | sort | uniq | wc -l #只输出行数
```

#### grep

输出模式匹配的行。

```bash
ls /bin /usr/bin | sort | uniq | grep zip
```

| 选项 | 含义                       |
| ---- | -------------------------- |
| -i   | 忽略字母大小写。           |
| -v   | 只输出不匹配指定模式的行。 |

#### head/tail

输出前/后10行内容。

```bash 
head ls-output.txt
tail ls-output.txt
```

| 选项 | 命令                                         |
| ---- | -------------------------------------------- |
| -n 5 | 输出前/后5行。                               |
| -f   | 持续观察该文件，有追加立即显示，直到Ctrl+C。 |

#### tee

读取标准输入，将输出结果写入标准输出或文件。

```bash
ls /usr/bin | tee ls.txt | grep zip
```

## Shell特性

### 扩展

#### 路径名扩展

```bash
echo D*
echo *s
echo [[:upper:]]*
echo /usr/*/share
echo -d .*
```

#### 浪纹线扩展

```bash
echo ~
echo ~foo
```

#### 算数扩展

```bash
echo $((2+2))
echo $(((5**2)*3))
```

#### 花括号扩展

```bash
echo Front-{A,B,C}-Back
echo Number_{1..5}
echo {01..15}
echo {001..15}
echo a{A{1,2},B{3,4}}b
```

#### 参数扩展

```bash
echo $USER
printenv | less #查看可用的变量列表
```

#### 命令替换

```bash
echo $(ls)
ls -l $(which cp)
file $(ls -d /usr/bin/* | grep zip)
```

### 引用

#### 双引号

防止单词分割。参数、算数、命令扩展仍会发生。

```bash
ls -l "two words.txt"
```

> **注意！**
>
> 以下写法会将输出结果中的换行视为分隔符，即包含38个参数的命令。输出结果将以一行形式出现。
>
> ```bash
> echo $(cal)
> ```
>
> 而以下写法输出格式正确：
>
> ```bash
> echo "$(cal)"
> ```

#### 单引号

同双引号，但禁用所有扩展。

#### 转义字符

略。

#### 反斜线转义序列

| 转义序列 | 含义     |
| -------- | -------- |
| \a       | 响铃。   |
| \b       | 退格符。 |
| \n       | 新行符。 |
| \r       | 回车符。 |
| \t       | 制表符。 |

可使用`echo -e`或`$''`解释反斜线转义序列。

```bash
sleep 10;echo -e "Time's up\a"
sleep 10;echo "Time's up" $'\a'
```

## 历史记录

```bash
history | less
history | grep /usr/bin
```

默认1000个命令，以下表示扩展第88行内容：

```bash
!88
```

### 历史扩展

```bash
!! #重复上一个命令
!number #重复第number个命令
!string #重复以string 开头的最后一个命令
!?string #重复包含string的最后一个命令
```

### script

记录整个Shell对话到文件file中：

```bash
script file
```

如果没有指定file，则保存在typescript中

## 权限

### id

查看用户身份信息：

```bash
id
```

文件属性：

| 属性 | 文件类型                              |
| ---- | ------------------------------------- |
| -    | 普通文件。                            |
| d    | 目录。                                |
| l    | 符号链接。                            |
| c    | 字符设备文件。例如：终端、/dev/null。 |
| b    | 块设备文件。例如：硬盘、DVD。         |

文件目录权限：

| 权限属性 | 文件                                     | 目录                           |
| -------- | ---------------------------------------- | ------------------------------ |
| r        | 读取                                     | 列出内容（且可执行）           |
| w        | 写入、截断；但删除、重命名由目录属性决定 | 创建、删除、重命名（且可执行） |
| x        | 作为程序执行                             | 进入目录                       |

### chmod

```bash
chmod 600 foo.txt
```

符号表示法：

| 符号 | 含义     |
| ---- | -------- |
| u    | 属主     |
| g    | 属组     |
| o    | 其他用户 |
| a    | all      |

用法示例：

```bash
chmod u+x
chmod u-x
chmod +x
chmod o-rw
chmod go=rw
chmod u+x,go=rx
```

选项：

```bash
chmod --recursive ...#对文件和目录都起作用
```

### umask

```bash
umask #查看默认掩码值 0002
umask 0000#更改掩码值
```

上述掩码值为16进制数，二进制位上为1的剥夺该权力。

### su

略。

### sudo

略。

### chown

更改文件属主和属组。

```bash
chown bob #属主更改为bob
chown bob:users #属主更改为bob，属组更改为users
chown :admins #属组更改为admins 属主不变
chown bob: #属主更改为bob 属组更改为bob的登录组
```

### chgrp

更改文件属组，已废弃。

### passwd

普通用户修改自己密码，或root修改其他用户密码。

## 进程

### 查看进程

静态查看：

```bash 
ps aux
```

STAT列字段说明：

| 状态 | 含义                                     |
| ---- | ---------------------------------------- |
| R    | 运行或准备运行。                         |
| S    | 睡眠，等待某个事件（按键、网络分组等）。 |
| D    | 不可中断的睡眠，等待磁盘设备I/O。        |
| T    | 已停止                                   |
| Z    | 已终止但未被其父进程清理的子进程。       |
| <    | 高优先级进程。                           |
| N    | 低优先级进程。                           |

其他列信息：

| 列名  | 含义                          |
| ----- | ----------------------------- |
| USER  | 进程属主，用户ID              |
| %CPU  | CPU占用率                     |
| %MEM  | 内存占用率                    |
| VSZ   | 虚拟内存大小                  |
| RSS   | 驻留集大小，占用RAM数量（KB） |
| START | 进程启动时间                  |

动态查看：

```bash
top
```

信息字段：

| 行   | 字段         | 含义                                                         |
| ---- | ------------ | ------------------------------------------------------------ |
| 1    | top          | 程序名称                                                     |
|      | 14:59:20     | 当前时刻                                                     |
|      | up 6:30      | 正常运行时间                                                 |
|      | 2 users      | 2个登录用户                                                  |
|      | load average | 平均负载，等待运行的进程数量，也就是可运行状态且共享CPU的进程数量。三个值分别为过去1min、5min、15min的平均值。低于1.0表示系统不繁忙。 |
| 2    | Tasks        | 进程数量及其状态                                             |
| 3    | Cpu(s)       |                                                              |
|      | 0.7%us       | 用于用户进程（内核之外进程）                                 |
|      | 1.0%sy       | 系统内核进程                                                 |
|      | 0.0%ni       | 低优先级进程                                                 |
|      | 98.3%id      | 空闲                                                         |
|      | 0.0%wa       | 等待I/O                                                      |
| 4    | Mem          | 物理内存                                                     |
| 5    | Swap         | 交换空间（虚拟内存）                                         |

键盘命令：h帮助，q退出。

### 进程置于后台

将xlogo进程置于后台：

```bash
xlogo &
```

使用以下命令查看shell在后台启动的作业列表：

```bash
jobs
```

使用以下命令将jobs编号为1的后台进程返回前台：

```bash
fg %1
```

停止/暂停进程：Ctrl-Z，恢复方法：使用fg或bg置于前/后台继续运行。

### kill

```bash
kill -信号 进程PID
```

常用信号：

| 代号 | 名称  | 含义                                            |
| ---- | ----- | ----------------------------------------------- |
| 1    | HUP   | 挂起。                                          |
| 2    | INT   | 中断，类似Ctrl-C。                              |
| 9    | KILL  | 杀死，最终手段，进程不能忽略。                  |
| 15   | TERM  | 终止。                                          |
| 18   | CONT  | 继续，SOPT或TSTP后恢复进程，bg和fg都会发送。    |
| 19   | STOP  | 停止，暂停，进程不能忽略。                      |
| 20   | TSTP  | 终端停止。                                      |
| 3    | QUIT  | 退出。                                          |
| 11   | SEGV  | 段错误，违规使用内存                            |
| 28   | WINCH | 窗口变化，例如top和less会相应该信号以调整界面。 |

### killall

向匹配指定名称或用户名的多个进程发送信号。

```bash
killall [-u user] [-signal] name..
```

例如：

```bash
killall xlogo
```

### 关闭系统

```bash
halt
poweroff
reboot
shutdown -h now #挂起
shutdown -r now #重启
```

### 其他进程相关

```bash
pstree
vmstat #静态
vmstat 5 #5秒刷新一次
xload #图形化
tload #终端
```

## 环境

### printenv

```bash
printenv | less
printent USER
echo $HOME
```

### set

```bash
set | less
```

### 常用环境变量

| 变量    | 内容                              |
| ------- | --------------------------------- |
| DISPLAY | 屏幕名称，通常:0                  |
| EDITOR  | 文本编辑器名称                    |
| SHELL   | Shell程序名称                     |
| HOME    | 主目录路径名                      |
| LANG    | 字符集及其所使用语言的排序方式    |
| OLDPWD  | 先前的工作目录                    |
| PAGER   | 对输出结果进行分页的名称          |
| PATH    | Shell搜索可执行程序名称的目录列表 |
| PS1     | 提示字符串1                       |
| PWD     | 当前工作目录                      |
| TERM    | 终端类型名称                      |
| TZ      | 指定时区                          |
| USER    | 用户名                            |

登录Shell会话启动文件顺序：

```
/etc/profile
~/.bash_profile
~/.bash_login
~/.profile
```

非登录Shell会话启动文件顺序：

```
/etc/bash.bashrc
~/.bashrc
```

字符串拼接：

```bash
foo="This is some "
echo $foo
foo=$foo"text."
echo $foo
```

改动生效：

```bash
source ~/.bashrc
```

## 定制提示符

### 转义字符

略。

### 增加颜色

文字颜色：`\033[0;30m`至`\033[0;37m`为黑、红、绿、棕、蓝、紫、青、浅灰，`\033[1;30m`至`\033[1;37m]`为深灰、浅红、浅绿、黄、浅蓝、浅紫、浅青、白。

背景颜色：同上，3\*换为4\*。

例如：

```bash
PS1="\[\033[0;31m\]<\u@\h \W>\$\[\033[0m\]"
```

### 移动光标

略。

## 存储设备

### 查看已挂载文件系统

法一：查看文件`/etc/fstab`。

法二：

```bash
mount
```

### 卸载文件系统

例如卸载挂载到`/dev/sdc`上的CD-ROM：

```bash
umount /dev/sdc
```

### 新建、删除挂载点

```bash
mkdir /mnt/cdrom
mount -t iso9660 /dev/sdc /mnt/cdrom
unmount /dev/sdc
```

### 手动挂载

例如：挂载16MB闪存。

```bash
sudo tail -f /var/log/messages
```

暂停后，插入闪存，并开始输出。发现“[sdb]”字样，符合SCISI磁盘设备名称

当出现以下两行时：

```
sdb: sdb1
sd 3:0:0:0: [sdb] Attached SCSI removable disk
```

即设备名称“/dev/sdb”，“/dev/sdb1”为其中第一个分区。

挂载：

``` bash
sudo mkdir /mnt/flash
sudo mount /dev/sdb1 /mnt/flash
df
```

### fdisk

示例：修改分区ID。

```bash
sudo umount /dev/sdb1 #fdisk前先卸载
sudo fdisk /dev/sdb
m #显示菜单
p #检查现有的分区布局
l #显示可能的分区类型长列表
t #修改分区ID并输入新的ID
w #写入修改后退出，也可不操作直接q退出
```

### mkfs

创建文件系统。

```bash
sudo mkfs -t ext4 /dev/sdb1 #ext4
sudo mkfs -t vfat /dev/sdb1 #fat32
```

### fsck

检查闪存驱动器。

```bash
sudo fsck /dev/sdb1 #先卸载
```

### dd

数据移动。

```bash
dd if=/dev/sdb of=/dev/sdc #两个驱动器之间移动
dd if=/dev/sdb of=flash_drive.img #驱动器复制成文件
dd if=/dev/cdrom of=ubuntu.iso
```

### genisoimage

创建ISO映像文件。文件在`~/cd-rom-files`中，输出文件名为`cd-rom.iso`。-R表示添加启用Rock Ridge扩展的元数据，允许使用长文件名和POSIX风格的文件权限。-J启用Joliet扩展，允许Windows使用长文件名。

```bash
genisoimage -o cd-rom.iso -R -J ~/cd-rom-files
```

### mount

直接挂载.iso文件，并进行md5检查。

```bash
mkdir /mnt/iso_image
mount -t iso9660 -o loop image.iso /mnt/iso_image
md5sum image.iso
md5sum /dev/cdrom
```

### wodim

擦除可刻录CD。

```bash
wodim dev=/dev/cdrm blank=fast
```

刻录映像文件。

```bash
wodim dev=/dev/cdrm image.iso
```

## 网络

### ping

```bash
ping linuxcommand.org
```

### traceroute

列出网络流量从本地到目标主机所经过的所有路由。

```bash
traceroute slashdot.org
```

星号隐藏即为打不通，-T或-I可显示。

### ip

取代ifoncifg。

```bash
ip a
```

### netstat

检查系统网络接口：

```bash
netstat -ie
```

显示内核网络路由表：

```bash
netstat -r
```

目的地中以0结尾的代表网络中的任意主机，最后一行default的Gateway表示网络流量最终的去处，即网关。

### ftp

示例：从FTP服务器fileserver的`/pub/cd_images/Ubuntu-18.04`下载.iso文件：

```bash
ftp filserver #用户名anonymous 密码随意 进入ftp对话
cd pub/cd_images/Ubuntu-18.04 #远程服务器目录切换
ls
lcd Desktop#本地路径切换
get ubuntu-18.04-desktop-amd64.iso
bye #退出ftp对话 同exit或quite
```

### wget

下载文件。

```bash
wget http://linuxcommand.org/index.php
```

### ssh

```bash
ssh remote-sys 
ssh bob@remote-sys #以bob用户身份登录
ssh remote-sys free #执行命令后返回本地
ssh remote-sys 'ls *' > dirlist.txt #远程执行扩展后结果保存到本地
```

### scp

示例：将remote-sys主目录下的document.txt文件下载到本地pwd中。

```bash
scp remote-sys:document.txt .
scp bob@remote-sys:document.txt . #指定bob用户
```

### sftp

用法同ftp。

## 查找文件

### locate

```bash
locate bin/zip #查找文件完整路径中存在子字符串bin/zip的文件
```

locate命令的数据库使用cron，为1天1更新，如果需要查询最新建文件，需要手动执行：

```bash
updatedb
```

### find

```bash
find ~ -type d | wc -l #查找主目录下属性为目录的项，wc统计行数
find ~ -type f -name "*.JPG" -size +1M | wc -l #大于1M的.JPG文件
```

-type可选参数：

| 文件类型 | 描述         |
| -------- | ------------ |
| b        | 块设备文件   |
| c        | 字符设备文件 |
| d        | 目录         |
| f        | 普通文件     |
| l        | 符号链接     |

-size计量单位符号：

| 字符 | 单位    |
| ---- | ------- |
| b    | 512字节 |
| c    | 字节    |
| w    | 字      |
| k    | KB      |
| M    | MB      |
| G    | GB      |

测试条件略。

操作符：

```bash
find ~ \(-type f -not -perm 0600\) -or \(-type d -not -perm 0700\)
```

预定义操作略。

自定义操作：

``` bash
find ~ -type f -name 'foo*' -exec rm '{}' ';' #类似-delete操作
find ~ -type f -name 'foo*' -exec rm '{}' + #将结果合并成一次输出结果
find ~ -type f -name 'foo*' -ok ls -; '{}' ';' #执行前确定
```

### xargs

从标准输入接收输入，转换为指定命令的参数列表。

```bash
find ~ -type f -name 'foo*' -print | xargs ls -l #同find的-exec选项
```

如果文件名包含空格，会将其视为参数分割。find的-print0生成由空字符分割的输出结果，xargs的-null或-0接受由空字符分割的输入：

```bash
find ~ -iname '*.jpg' -print0 | xargs --null ls -l
```

### touch

设置或更新文件的访问、变更、修改时间，常用于创建文件。

```bash
touch playground/dir-{001..100}/file-{A..Z}
```

## 归档与备份

### gzip

```bash
gzip foo.txt #将foo.txt压缩为foo.txt.gz 源文件不保留
gunzip foo.txt #将too.txt.gz解压为foo.txt
ls -l /etc | gzip >foo.txt.gz
gunzip -c foo.txt | less #不解压，查看foo.txt.gz的内容
zcat foo.txt.gz | less #同上
```

### bzip2

```bash
bzip2 foo.txt
bunzip2 foo.txt.bz2
```

### tar

```bash
tar cf playground.tar playground #将playground目录归档为playground.tar
tar tvf playground.tar #列出归档的详细清单
tar xf ../playground.tar #提取归档内容
find playground -name 'file-A' | tar czf playground.tgz -T - #-T从文件读取列表，创建gzip文件
find playground -name 'file-A' | tar cjf playground.tbz -T - #创建bzip2文件
ssh remote-sys 'tar ct - Documents' | tar xf -
```

### zip

```bash
zip -r playground.zip playground
unzip ../playground.zip
unzip -l playground.zip playground/dir-087/file-Z #-l只列出而不解压
unzip playground.zip playground/dir-087/file-Z #如果已经存在会询问是否覆盖
find playground -name 'file-A' | zip -@ file-A.zip #-@输入通过管道传入
ls -l /etc/ | zip ls-etc.zip -
unzip -p ls-etc.zip | less #-p管道
```

### rsync

```bash
rsync -av playground foo --delete #playground文件夹备份到foo文件夹 -a递归并保留属性 -v详细信息 --delete如果文件不存在，则在旧备份中删除，否则保留
rsync -av --delete --rsh=ssh /etc /home /var/local remote-sys:/backup
```

