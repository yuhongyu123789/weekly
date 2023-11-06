# 2023-04-11-第五周

## 本周总结

### 搞咗乜嘢

- 手动脱壳小技巧：找到OEP，并且修复表，获得正常的入口。
- GDOUCTF，看了大上午，只搞了个多元方程……Tea算法现学，找不到加密的密文，找到key和脚本了。
- 《逆向工程核心原理》，跟着教程一步一步看程序，很多操作很神奇，第二章看完了。基础操作还是很实用的。
- 

### 新知识

#### 计算机中Dump的含义

Dump的本意是"倾卸垃圾"、"把(垃圾桶)倒空"。在计算机技术中使用Dump的主要意思依然如此,即当电脑运行出现故障而无法排除时,通常要重新启动。为了找出故障的原因,需要分析现场(即出现故障时整个内存的当前状况),在重新启动系统之前需要把内存中的一片0、1(这时它们尤如一堆垃圾)"卸出"保存起来,以便由专家去分析引起故障的原因。

> 1. *n*. A collection or recitation of allavailable information about a problem (as in: "I need a quick dump on thatissue."). 
>
> 2. *v*. To record, at aparticular instant, the contents of all or part of one storage device inanother storage device. Dump data is extremely useful when debugging theproblem. 
> 2. *n*. Data that has beendumped. 
> 2. *v*. To copy data in areadable format from main or auxiliary storage onto an external medium such astape, diskette, or printer. 
> 2. *v*. Tocopy the contents of all or part of virtual storage for the purpose ofcollecting error information.
>
> **译文：**
>
> 1. *名词*。收集或逐一列举关于某个问题的所有可用信息（如：“我需要一个关于该问题的快速转储”。）
>
> 2. *动词*。在特定时刻，将记录或数据从一个存储设备转储到另一个存储设备上以保护数据。在调试问题时，转储数据非常有用。
>
> 3. *名词*。已转储的数据。
>
> 4. *动词*。以可读格式将数据从主存储器或辅助存储器复制到外部介质上，如磁带、软盘或打印机。
> 4. *动词*。为了收集错误信息而复制全部或部分虚拟存储器的内容。

由此可见，dump可作为动词也可看作名词。作为动词时宜译为"转储"。作为名词时，可将经转储而产生的那些数据（内容）称作dump，这些数据实际上就是内存中由一片0、1组成的map(映像),因此,这时的dump应译为"内像"(内存中的映像)。

比如以前人们在IBM主机系统中做dump时,通常是转储到磁带上,所以有人把这盘磁带也叫dump。为了便于阅读与分析,把内像按既定的格式打印在纸上,人们便把这一堆打印纸也叫dump。为了实现以上二项工作,必须有相应的程序,人们把这种程序也叫dump,实为dump routine的简写。

```
在Linux系统中有一些带dump字样的命令，如：
dump：用于备份文件系统
hexdump：用来查看文件的十六进制编码
tcpdump：转储网络上的数据流
objdump：查看目标文件的信息
```

#### 新工具：LordPE和Import REC（脱壳工具）

#### OEP是什么

在[新手必须懂得什么叫OEP，以及查找的常用方式软件破解什么是oep?](../OEP.md)这篇文章中解释，并且别人还给出了另外几种有种（但我还没有尝试）的方法。

## CTF-REVERSE练习之脱壳分析

打开看到程序需要脱壳。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304131954821.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

PEiD可以看到UPX加壳。在自己机子上可以直接用图形化的软件脱壳。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304131955863.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

这里使用Ollydbg进行脱壳。打开软件后可以看到页面停止在Pushad函数上。从这里步进观察ESP寄存器，并“在数据窗口中跟随”。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132005304.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

在数据窗口中选择`0012FFA4`的前四个字节，右键`断点-硬件访问-Dword`。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132013842.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

接下来按下F9运行程序，程序运行一段时间后OD将自动断下，这时候先删除之前设置的硬件断点，依次选择菜单项中的`调试——硬件断点`，删除我们设置的硬件断点。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132025677.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132026433.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

随后可以开始F7步进，让程序运行到0043FD24处。继续步进，跳到004094F8处。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132029178.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132039478.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

在反汇编指令窗口中单击鼠标右键，选择“Dump debugged process”菜单项，在弹出的OllyDump窗口选择“Dump”按钮保存文件。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132043302.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

后面还需要在OD中进行修复操作。**对UPX脱壳而言，进行到这一步就可以了**，但是如果是另外一些壳，可能还需要对程序的输入表进行修复操作。

现在需要**修复脱壳后的程序**，用Import REC打开，然后做一大堆（完全不懂为什么的）步骤。其中OEP（original entry point 原始入口点）填入前面找到的入口点信息。

首先在进程列表中选择`C:\Reverse\6\crackme6.exe`，然后在OEP中填入94F8（也就是在OD中找到的一个信息），然后点击“IAT AutoSearch”按钮，接着点击“GetImports”按钮，就可以看到程序的输入表信息了。

点击右侧的“Show Invalid”按钮，看看是否存在无效的输入表项目。无效的输入表项目前面带有问号（？），如果有可以使用右键菜单删除。这里没有无效的输入表项目，所以选择“Fix Dump”按钮，对我们的dumped.exe进行修复，得到dumped_.exe程序。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132121521.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132123000.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304132158778.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

## CTF-REVERSE练习之病毒分析

### 知识点

#### 7Zip

7-Zip 是一款开源软件。我们可以在任何一台计算机上使用 7-Zip ，包括用在商业用途的计算机。7-Zip 适用于 Windows 7 / Vista / XP / 2008 / 2003 / 2000 / NT / ME / 98。并且有面向 Mac OS X、Linux、Unix 平台的命令行版本。

7zip使用起来十分方便，通过添加的右键菜单，可以尝试对任意文件进行解压缩操作。7zip支持的文件格式十分丰富，其中压缩包括：7z, XZ, BZIP2, GZIP, TAR, ZIP and WIM等格式，解压缩包括：ARJ, CAB, CHM, CPIO, CramFS, DEB, DMG, FAT, HFS, ISO, LZH, LZMA, MBR, MSI, NSIS, NTFS, RAR, RPM, SquashFS, UDF, VHD, WIM, XAR, Z等格式。

在一些CTF逆向分析的题目中，我们可以尝试使用7zip对其进行解压缩操作，可能就会有意想不到的效果，可以大大加快我们的分析过程。

#### 在线沙箱

网上有许多公开的在线沙箱，使用这些沙箱提供的服务，我们可以方便的观察一个程序的详细行为报告，进而判断一个程序大致的内部逻辑。

在线沙箱通常用于大致判定一个程序的行为是否安全，在逆向分析中，我们可以通过提交一个文件给沙箱程序来判断程序内部的大致逻辑，通过对沙箱报告的分析，有时候可以有效加快我们的逆向分析进程。

常见的在线沙箱包括但不限于：

```
VirusTotal：https://www.virustotal.com/gui/
VirSCAN：https://virscan.org/
微步云沙箱：https://s.threatbook.cn/
Joe Sandbox Cloud Basic：https://www.joesandbox.com/#windows
布谷鸟沙盒：https://sandbox.pikker.ee/
OPSWAT MetaDefender：https://metadefender.opswat.com/?lang=en
```

### 题目和操作

> **题目描述：**
>
> 某日，一小学生弄了个U盘到打印店打印文件，U盘往计算机上一插，发现机子死机了，高明的打印店老板为了防止此类事件，特意设置了霸王键，可一键备份，随后老板把U盘备份了交给小王，小王想要知道U盘里到底被感染了什么你能帮帮他吗？
>
> 主机C:\Reverse\8目录下提供了这个UP_BOOT.img文件，请对该文件进行逆向分析，找到题目过关的Flag。

题目是一个有病毒的U盘文件。这个文件是img文件，那么按照提示就用7Z解压。其中一个文件看着就是一个不想让我们通关的游戏。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304142033630.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

但另一个文件打开就是战书……看来就还是需要继续分析这整个软件。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304142035838.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

继续解压会发现还有三个文件。其中两个可执行就需要重点关照。其中1.exe的文件打开后命令行闪过，非常可疑。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304142043189.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

用安天的分析工具可以看到底发生了甚么。分析之后有一个test.txt的文本文件。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304142049168.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

看来是运行的时候隐藏起来了，直接看隐藏文件就行。出现内容`WdubQ4IGEzAG54NfATJTNhI4TLIvPvENyTLLWb3YCNBeK5wad5XCgrSQNOih1F`

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304142051118.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

取16位的md5就好啦。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304142054056.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)



## BUUCTF

### [FlareOn4]login

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304170812033.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

一眼Rot13，但是需要看到正则`/[a-zA-Z]/g`只取字母，不用符号变换。

```python
galf='PyvragFvqrYbtvafNerRnfl@syner-ba.pbz'
flag=''
for i in galf:
    if ord(i) in range(78,91) or ord(i) in range(110,123):
        flag+=chr(ord(i)-13)
    else:
        flag+=chr(ord(i)+13)
print(flag)
```

得到`ClientSideLoginsAreEasyMflare:on;com`，符号不换就是`flag{ClientSideLoginsAreEasy@flare-on.com}`。

