# 2023-03-19-第三周



## 本周总结

Re学习终于走向正轨，但是发现基本的知识点还是没有掌握熟练。Re主要还是工具的运用，前面的知识点仍有需要复习的地方。

### 学咗乜嘢

学了些常见的窗口程序的api，以及如何用OD找到

OD中JUMP、CALL一些简单常见函数

IDA中文搜索插件查找ascii

一些字符串替换的简单re可以直接od调试出来，别写脚本

脱壳插件作用，地址重新定位

Docker的一些基本方法，还有方便易用的Docker Compose。虽然Re用不到。

### 下周待做事项

新工具 - Restorator的使用，直接查找程序内的字符串和图片资源。

上课的时候听不下了就看点re的书，至少我的深入理解计算机操作系统还没能继续坚持看下去

在继续在合天上多学几节课

### 知识分享

#### MessageBox

MessageBox是一个阻塞的API，就是当调用这个API的时候，会弹出一个消息框，此时程序的代码执行流就会**自动阻塞在调用MessageBox的地方，直到点击提示框上的按钮或者关闭提示框**时，程序才会继续往下执行。MessageBox的这个功能可以快速定位关键函数代码。

#### Restorator

这个软件可以直接查看程序内的各种信息，包括字符串、对话框、图标等内容。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303211959498.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### 情感、思考、观点

这周学的太少了。有的知识甚至在自己出题的时候才去看到的。麻了。

有时我无法静下心来，总想看看些新奇的玩意。

宿舍学习环境真的很重要啊，如果没法大家一块学，学习上没法统一战线，不如单兵作战。虽然说团队意识很重要，但有一个人解决问题的能力也很重要啊。

有没有一种可能，Misc用得到动态容器，比如容器内实时生成一个随机隐写的图片和视频。

---

## 【合天】CTF-REVERSE练习之API定位

### 外部行为分析

开始前需要观察程序的运行过程，可以输入字符的地方等等。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201223096.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

还有查壳，了解程序信息。

y0ung说熟悉了之后可以直接在IDA中的`view`看到加壳和没加壳的程序的区别，但是很显然我没有到那种境界。(樂)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201224990.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

没加壳。

### 动态调试

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201937639.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

也没有找到`错误`的字符串。需要寻找`MessageBoxA`模块来定位弹出窗口的代码位置。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201940845.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201942489.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201943899.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

就可以运行程序查找断点运行代码时候的状态。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201947777.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

此时可以查看断点附近的信息，找到相关代码。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201949854.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303201953564.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### 静态分析

使用IDA32（在前面已经看到是GUI32的应用了）

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303202020786.png?imageMogr2/quality/70/strip|watermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10|watermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303211936077.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

教程在这里用了前面OllyDBG的分析知道函数sub_401450是我们需要的函数。因为这个`MessageBoxA`前有很多需要LoadString的函数调用。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303211948358.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

而且辣鸡教程仍然直接写出了`0x6Au`就是最终密码，难道这个函数有什么特殊的地方嘛？

但是实际过程中仍然需要自行查找每个Box上下文。不如就直接用OllyDBG动态分析。

这里教程还给了一个`Restorator`的软件，意在直接查找软件内的字符串（寻思着直接找不就好了吗）

### Restorator

这个软件可以直接查看程序内的各种信息，包括字符串、对话框、图标等内容。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303211958515.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![软件内的图标也可以直接查看](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303211959498.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### 待解决问题

- 右侧的`STRING`内的字符串能否直接搜索查找？
- 获得新工具 - Restorator

::: info

1. 仔细观察关键函数代码附近的API调用，请问还可以通过哪些API来定位到校验密码的函数？
2. 在IDA中通过对MessageBeep查找交叉引用来定位密码校验函数
3. 在OD中通过对LoadStringA下API参考断点来定位密码校验函数

:::

## 【合天】CTF-REVERSE练习之API断点

## 知识点

**输入表与API动态调用**

在逆向分析中经常会遇到输入表这个概念，输入表中保存的是在程序中调用的但定义在其他DLL中的函数信息以及对应的DLL信息。我们在程序中直接调用Windows API的时候，这些API都可以在程序的输入表中可以看到。

使用PEiD可以方便的查看程序的输入表结构，将程序载入PEiD后，点击主界面中的“子系统”，在弹出的“PE细节”中点击“输入表”就可以打开“输入表查看器”了，如图所示：

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303212009739.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

API定位中使用的方法其实就是通过输入表中的API来定位关键函数代码的。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303212009741.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

虽然输入表中可以看到程序使用了哪些API，进一步通过OD的API参考断点，或者是IDA中的交叉引用就可以定位到程序在哪些地方使用了这些API，但是我们也可以通过动态调用的方式来消除输入表中的API特征。

通过配合使用**LoadLibrary**以及**GetProcAddress**，我们就可以动态获取API函数的地址了，其中LoadLibrary用于加载动态链接库，GetProcAddress用于获取指定动态链接库中指定API函数的地址。动态调用的API无法通过OD的输入表API参考断点或者IDA的交叉引用来进行定位。

### API断点

在OD中可以对特定的API设置一个断点，不管是直接调用的API还是动态调用的API。

在OD的反汇编指令窗口中按下`Ctrl+G`快捷键，在弹出的对话框中输入`MessageBoxA`，单击“确定”按钮后就可以来到MessageBoxA的函数代码了，我们对其第一条指令设置一个断点，即在77D507EA按下`F2`即可。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303212009766.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

在OD左下角的Command窗口中输入`bp`命令也可以下断点，比如输入`bp MessageBoxA`，按下`Enter`就可以对MessageBoxA设置一个断点了。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303212009719.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### MessageBox

MessageBox是一个阻塞的API，就是当调用这个API的时候，会弹出一个消息框，此时程序的代码执行流就会**自动阻塞在调用MessageBox的地方，直到点击提示框上的按钮或者关闭提示框**时，程序才会继续往下执行。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303212009774.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

利用MessageBox的这个特性，也可以帮助我们快速定位关键函数代码。

### OllyDBG 实操

这次打开OD后未能使用`右键-查找-当前模块中的名称（标签）`的方式找到MessageBoxA函数，即使找到后也无法在F9的调试中成功截断。

题目说这个程序不再直接调用MessageBoxA这个API，因此无法成功截断。需要使用另一种方法：使用`Ctrl+G`定位MBA代码，再用F2设置断点。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303212123553.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

> Ctrl+G：转到某地址。该命令将弹出输入地址或表达式的窗口。该命令不会修改EIP。

接下来按`F9`运行调试，随便输入一个字符然后确定。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222004794.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

此时在OD中`调试-执行到用户代码`才能弹出提示密码错误的窗口。然后点击确定，可以停在关闭窗口之后的函数。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222007609.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222010236.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

接下来需要F8步入，查看状态框的信息。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222047057.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### IDA静态分析

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222055017.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

在这个位置F5之后可以找到对应的伪代码，可以看到`strcmp`函数。说明需要`&String`和`HeeTianLab`比较得出需要打开的窗口。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222105818.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

### 待解决的问题

- 为什么字符串在`call`之后就可以找到？
- 这里的程序逻辑是什么？

::: info

1. 尝试使用Restorator查看CrackMe3.exe的字符串资源，你能看到里面有密码吗？
2. 尝试在OD中对LoadStringA下断点来跟踪关键函数代码
3. 如果由你编写这个CrackMe程序，你会怎么对抗逆向分析？

:::

## 【合天】CTF-REVERSE练习之算法分析1

### 知识点

#### PEiD密码算法分析插件

不管是在CTF竞赛的REVERSE题目中，还是在实际的商业产品中，很多程序都喜欢使用成熟的标准算法来作为注册算法的一个部分，如MD5、Blowfish等。这些算法本身往往就十分复杂和难以理解，如果从反汇编指令来阅读这些算法则更是难上加难。对于标准算法，实际上我们并不需要知道这些算法的详细计算过程，我们只需要知道是哪一个算法即可，因为标准算法网上都能找到成熟的库文件或者源码等。

PEiD有一个叫做Krypto ANALyzer的插件，使用这个插件可以对程序进行扫描，通过特征匹配来识别程序内部可能用到的一些标准算法。Krypto ANALyzer的使用方法为：点击PEiD主界面右下角的“=>”按钮，选择“插件”菜单项，然后选择“Krypto ANALyzer”，就可以弹出Krypto ANALyzer插件了。Krypto ANALyzer插件会自动分析程序内部可能用到的标准算法，如图所示：

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303282025721.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

下图中显示了程序中在地址00401E5C处存在MD5算法的特征：

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303282026772.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

#### IDA重命名等功能

在IDA中，我们可以通过按下N键来对一个变量/函数/标记等进行重命名操作，函数和变量命名对于帮主我们理解程序的内部逻辑非常重要，就好比我们在编程的时候，培养良好的编程风格非常重要一样。

比如如果函数`sub_4012E0`经过我们分析之后，确定其功能为将传入的字符串转为大写形式，那么我们可以选中`sub_4012E0`后按下N键对其进行重命名（将函数名命名为`fnStringToUpper`）：

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222118353.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

IDA通过还可以给汇编指令或者伪代码来添加注释。如果要对某一条汇编指令添加注释，只需要在汇编指令所在行按下封号（即`;`）即可弹出对话框来接收注释；如果要给伪代码添加注释，则只需在伪代码所在行按下斜杠（即`/`）即可弹出对话框来接收注释。

OD也可以给汇编指令添加注释，只需要在汇编指令所在行后一列的空白处双击鼠标左键即可，如图所示：

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202303222118337.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)
