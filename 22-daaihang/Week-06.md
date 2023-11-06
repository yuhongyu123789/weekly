# 2023-04-18-第六周 / 04-25-第七周

## baby.bc

### （一）bc文件构成

用file命令看了文件，发现是个LLVM文件。[Bitcode](https://llvm.org/docs/BitCodeFormat.html)是[LLVM](https://llvm.org/docs/index.html) IR的二进制形式。看来这个形式与Bitcode有关。

Bitcode本质上就是一个比特序列，或者叫做[比特流](https://llvm.org/docs/BitCodeFormat.html%23bitstream-format)。LLVM 编译器基础结构支持广泛的项目，从 工业强度编译器到专用 JIT 应用程序到小型 研究项目。

整个bitcode和包装格式都是采用小端字节序，每32个比特为一组，称为一个word。

> 举例来说，数值`0x2211`使用两个字节储存：高位字节是`0x22`，低位字节是`0x11`。
>
> > - **大端字节序**：高位字节在前，低位字节在后，这是人类读写数值的方法。
> > - **小端字节序**：低位字节在前，高位字节在后，即以`0x1122`形式储存。
> >
> > ![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202305020050256.jpeg?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)
> >
> > 为什么会有小端字节序？计算机电路先处理低位字节，效率比较高，因为计算都是从低位开始的。所以，计算机的内部处理都是小端字节序。
> >
> > 但是，人类还是习惯读写大端字节序。所以，除了计算机的内部处理，其他的场合几乎都是大端字节序，比如网络传输和文件储存。

*.bc文件实际上是[bitcode包装格式](https://llvm.org/docs/BitCodeFormat.html%23bitcode-wrapper-format)，其内容主要分为两部分：header和body。文件开头的20个字节是header，真正的bitcode数据则是body。Header的内容是5个32比特整数，依次表示魔数、版本号、bitcode偏移量（单位是字节）、bitcode字节数、CPU类型。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202305020102736.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

**目前可以得出，魔数是：0xDEC04342（如果按大端排序看起开有个`code`字符），版本号：0x1435，bitcode偏移量是0x05，字节数是0x24300C62，CPU类型是0xE6BE5949。header之后紧接着就是bitcode数据。**



---

目前是读了[LLVM Bitcode格式介绍（一） - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/308201373)这篇文章，基本没有读懂。但是发现可以按照这个方法用010看懂llvm的结构，似乎没法继续用这种方法继续读懂程序，并且题目中需要我的input，那必然要看源代码。遂看了WP（也没搞懂）。大致需要转成.s文件，然后重新转回.c文件用ida分析。

但是gcc编译出了点问题，决定下次看题再解决一下。

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202305020129708.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)

![](http://picgo-1258675557.cos.accelerate.myqcloud.com/202305020129431.png?imageMogr2/quality/70/strip%7Cwatermark/2/text/wqkgMHhmLiBEYWFpaGFuZyBXb25n/font/U1RIZWl0aSBMaWdodOWNjuaWh-m7keS9ky50dGM/fontsize/18/fill/I2ZmZmZmZg/dissolve/70/shadow/40/gravity/southeast/dx/10/dy/10%7Cwatermark/3/type/3/text/V2FuZ0RhaGVuZ0RhYWloYW5nV29uZ1dESA)8.

### （二）.s文件

#### GCC -S选项：生成汇编文件

编译器的核心任务是把C程序翻译成机器的[汇编语言](http://c.biancheng.net/asm/)（assembly language）。汇编语言是人类可以阅读的编程语言，也是相当接近实际机器码的语言。由此导致每种 CPU 架构都有不同的汇编语言。

> 实际上，[GCC](http://c.biancheng.net/gcc/) 是一个适合多种 CPU 架构的编译器，不会把C程序语句直接翻译成目标机器的汇编语言，而是在输入语言和输出汇编语言之间，利用一个中间语言，称为 RegisterTransfer Language（简称 RTL，寄存器传输语言）。借助于这个抽象层，在任何背景下，编译器可以选择最经济的方式对给定的操作编码。
>
> 而且，在交互文件中针对目标机器的抽象描述，为编译器重新定向到新架构提供了一个结构化的方式。但是，从 GCC 用户角度来看，我们可以忽略这个中间步骤。

通常情况下，GCC 把汇编语言输出存储到临时文件中，并且在汇编器执行完后立刻删除它们。但是可以使用`-S`选项，让编译程序在生成汇编语言输出之后立刻停止。

如果没有指定输出文件名，那么采用`-S`选项的 GCC 编译过程会为每个被编译的输入文件生成以`.s`作为后缀的汇编语言文件。