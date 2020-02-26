---
title: "解决github下载速度慢的难题"
date: 2020-02-23T19:32:42+08:00
draft: true
---

# 修改host文件

1. 找到hosts文件

在macOS环境下，host文件位于
/etc/hosts

通过sudu vim /etc/hosts来修改文件

2. 查找github使用的动态IP

    [查找IP](https://www.ipaddress.com/)


    获取如下三个网址的IP地址

    - github.com

    - github.global.ssl.fastly.net


    并且写入到hosts中

3. 刷新网络

    `sudo killall -HUP mDNSResponder`


只能说是一种方法，但未必有用。