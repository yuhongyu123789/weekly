# 2023-04-01-第四周

## 本周总结

### 学咗乜嘢

这周出了两道re题（准确地说是一道半，因为第二道发现上不了难度就去看了XOR），还有3道图一乐的Misc。合天的课有点小摆，算法开始难懂了。中途看了一点java，看了某个多年前学长的教务系统的爬虫项目，修了个代码成功搞定爬虫。Java感觉和Python差不多，但是更难（悲。

### 下周待做事项

先学会怎么用IDA吧，不然真尼玛看不懂为啥教程是那种思路。

合天快到期了，看看逆向工程核心原理（好贵），Y0ung推的，小西巴的一个大佬写的。

### 知识分享

### UPX

> 雷总出的题加了壳，发现我脱壳脱不干净，后来知道是用最新的UPX加的，导致没法动态调试。（感觉动态也没那么好用……）

UPX (the Ultimate Packer for eXecutables)是一款先进的可执行程序文件压缩器。压缩过的可执行文件体积缩小50%-70%，这样减少了磁盘占用空间、网络上传下载的时间和其它分布以及存储费用。通过 UPX 压缩过的程序和程序库完全没有功能损失，和压缩之前一样可正常地运行。对于支持的大多数格式没有运行时间或内存的不利后果。

UPX 支持许多不同的可执行文件格式，包含 Windows 95/98/ME/NT/2000/XP/CE 程序和动态链接库、DOS 程序、Linux 可执行文件和核心等。

按照壳的功能特性，壳可以划分为压缩壳和加密壳，压缩壳侧重于压缩体积，加密壳侧重于加密，二者的出发点是不一样的。常见的压缩壳有upx、ASPack等，常见的加密壳有ASProtect、Armadillo等。

### 情感、思考、观点

上周摆了，这周开始实习就还好。

最近想吃水果了但这里水果好贵，买不起了的感觉……

为什么感觉最近周围人都也开始搞CTF，可能是上周比赛大家的热情很高吧。

## A&D四月常规赛复盘

### ezRe

方法一：看IDA伪代码，写解题脚本。（费时，不看题）

题目源码：

```c
#include <stdio.h>
#include <string.h>

int main() {
	char flag[] = "jrTT?lRrR.rnj_WrF.Li_gr\"R_pp?/j";
	char input_string[40];

	for (int i = 0; i < strlen(flag); i++) {
		flag[i] += 2;
		if (flag[i] == 97)
			flag[i] = 64;
		if (flag[i] == 116)
			flag[i] = 95;
	}
	printf("Input the flag without \'flag{}\': ");
	gets_s(input_string, 40);

	if (strcmp(flag, input_string))
		printf("you are wrong, see again!\n\n");
	else
		printf("you are right!\n");
	system("pause");
	return 0;
}
```

方法二：（预期解法）用OD动态调试，快速方便。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111857194.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

关键词“wrong”，则题目需要输入正确的FLAG。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111858214.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111858836.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

插件 -> 中文搜索引擎 -> 搜索ACSCII。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111858862.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

可以直接找到“wrong”和“right”关键语句，便就直接Follow到那个字符串位置。并F2设置断点。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111859830.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

开启调试，随意输入字符后回车。OD在断点停下，找到ASCII字符串。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111859339.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111900259.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

`flag{l_VVAnT_T0_pl@Y_H0Nk@i_$T@rrA1l}`

> 另外出了几个简单的杂项，图一乐。

## 盯帧

直接用StegSolve工具查看分层。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901368.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

## Vahalla

先看题目，寻找头像的奥秘。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901969.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

点击“这里”可以进入排行榜界面，找到名为“Vahalla”的战队，寻找它的头像。并且右键保存战队头像。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901914.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom: 80%;" />

用010Editor打开，直接用`Ctrl+F`搜寻字符串“Flag”，回车找到flag，但是是加密过的。结尾的等号很明显，这是Base64加密。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901503.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" alt="image-20230408234054285"  />

直接在线离线解密都可以，得出正确flag。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901742.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

## 文明用语

看题可以得出我们需要知道她说了什么。而且是倒过来的，那说明图片宽高被修改了。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901987.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901272.jpeg?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

打开010Editor修改宽高。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901510.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

我们调大图片宽高，保存后就可以看到图片被截取的信息。旋转后得出flag。

<img src="http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111901721.jpeg?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA" style="zoom:50%;" />

## 新知识点

### libnum库

libnum.s2n()将字符串转换为数字；

相反的，libnum.n2s()将数字转换为字符串。

~~~python
import libnum
print(libnum.s2n("0xf.Daaihang"))

Output: 15000834104637125466876112487
~~~

libnum.s2b()将字符串转换为二进制字符串；

相反的，ibnum.b2s()将二进制字符串转换为字符串。

### IDA Graph View

> [IDA-数据显示窗口（反汇编窗口、函数窗口、十六进制窗口）](https://blog.csdn.net/tabactivity/article/details/78492371)
>
> 有时候需要看Graph View但总是对每个框的内容不甚理解。还得先读懂工具先吧。

IDA使用不同的彩色箭头区分函数块之间各种类型的流。在条件跳转位置终止的基本块可能会生成两种流：Yes边的箭头（是，执行分支）默认为绿色，No边的箭头（否，不执行分支）默认为红色。只有一个后继块的基本块会利用一个正常边（默认为蓝色）指向下一个即将执行的块。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062142371.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062146632.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

如果想要查看更完整的汇编代码，那就选中一个代码框，右击选择Text View即可。

### Restorator(也許是舊的新知識)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111913315.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### 异或运算

异或运算Exclusive OR，常常缩写成XOR。异或运算针对二进制0和1而言，数学符号⊕，**异或运算的法则：0⊕0=0，1⊕0=1，0⊕1=1，1⊕1=0**。

除了二进制以外，异或运算可以扩展到任意数据类型。因为我们所接触的任何数据都可以使用二进制来表示，所以可以对二进制里面的所有位上的数据都进行两两的抑或操作，如15的二进制表示为1111，而100的二进制表示为1100100，因此15⊕100的计算过程如下：

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304111921860.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

**异或运算的性质：A⊕B=B⊕A，A⊕A=0，A⊕0=A，A⊕B⊕A=0⊕B=B。**

在编程语言中，通常使用^符号来表示异或运算（C & Python）。

## 【合天】CTF-REVERSE练习之算法分析1

> 知识点在上周。这道题感觉好难，看了真的好久。做不下先看了看Graph View怎么用的。

程序还是一个输密码看对错的程序。但是这次需要仔细阅读代码。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062029465.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

这里的长度是判断33，但解释说其实是32的长度，IDA反编译错了（？）。

这里的`0xA`应该是数字，因此右键可以更改显示的类型。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062100213.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

可以在`Graph View`中找到这段代码。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062103807.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

（但是仍然不懂如何找到这段代码，以及看得出来这段代码在`Graph View`的作用。）

> 通过上面的伪代码的分析，我们发现只有`sub_401510`这个函数的功能并不清楚，通过双击`sub_401510`查看对应的伪代码，发现有点复杂，暂时无法理解，不过这并不要紧。

要紧，问题大的很。

### 接下来需要用PEiD来睇睇乜事了

需要用Krypto ANALayzer插件，很快看出来是用了MD5注册算法。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062113318.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

得到00401E5C的地址后，我们可以在IDA中的`IDA View`窗口按G键，直接查找这个MD5函数所在的位置。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202304062120666.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

