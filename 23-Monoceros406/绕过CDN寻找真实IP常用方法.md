---
title: 绕过CDN寻找真实IP常用方法
date: 2024-01-01 10:07:31
tags: 渗透测试
mathjax: true
---

# 绕过CDN寻找真实IP常用方法

1. 通过目标网站用户注册或RSS订阅，查看邮件、寻找邮件头中邮件服务器域名IP。
2. 扫描网站测试文件，如phpinfo、test
3. 分站可能没有挂CDN，ping二级域名获分站IP，可能分站和主站不是同一个IP但在同一个C段。
4. 国内CDN只对国内用户访问加速，国外CDN不一定，通过国外在线代理网站App Synthetic Monitor访问。
5. 目标很久以前可能没有用过CDN，通过NETCRAFT观察域名IP历史记录。
6. 如果网站有自己的APP，Fiddler或bp抓包。
7. 尝试通过CloudFlareWatch对客户网站进行真实IP查询。
