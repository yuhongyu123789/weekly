---
title: Artfuscator使用方法
date: 2024-01-13 20:28:26
tags: 逆向工程
mathjax: true
---

# Artfuscator使用方法

## 安装

```bash
git clone https://github.com/JuliaPoo/Artfuscator
cd Artfuscator
git submodule update --recursive --init --remote
cd elvm
make art #事实证明报错也问题不大
cd ..
```

## 使用

在Artfuscator的目录下放置C语言文件，例如hewwo.c。再执行：

```bash
make hewwo IMG=etc/niko-grey.png
```

其中，选择的图片为当下目录的etc/niko-grey.png。图片要求单通道灰度图片。

生成的文件在dist文件夹下，名称为hewwo.art的可执行文件。
