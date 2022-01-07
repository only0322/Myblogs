---
title: "Electron打包简易教程"
date: 2022-01-07T10:39:31+08:00
draft: true
---

## 一、工具选用

electron-packager 或electron-builder都可以，但个人还是更喜欢electron-packager

## 二、命令

`electron-packager . Tal --platform=darwin --arch=x64 --icon=./Tal.ico --out=./out  --app-version=0.01 --overwrite`

这条命令是在当前目录下进行打包，打包的平台是MacOS，x64架构，然后设置了版本号，ico图片，输出目录。overwrite则是会覆盖上次打包的内容。

win10下打包： `electron-packager . Tal --platform=win32 --arch=x64 --out=./out --icon=./Tal.ico  --app-version=0.01 --overwrite --electron-zip-dir=../` 

`--electron-zip-dir`可以指定electron的zip包，避免下载缓慢的问题。

`--ignore`可以指定打包忽略的目录

## 三、一些遇到的问题及处理

1. 打包特别慢，直接卡死

其实不是卡死了，而是因为node_modules的东西太多了，至于为什么太多了，可以看下一条。

2. 打包后软件800M，但实际上没什么复杂的内容

主要原因是因为我们用**cnpm**去安装了模块，导致模块都是散在外面的，然后打包又慢又大，**用npm和yarn就能解决**。

3. 打包后提示缺少模块，比如vue

缺模块是不可能的，尤其不可能缺少前端框架，尝试参数里面删掉 --ignore=node_modules 这种命令。

4. 打包后白屏

解决方式有两个：

- 百度下怎么打包后也能打开控制台，看看报错

- 打包命令去掉 --asar ，这个好像是给包加密的，一般没什么用。

其他问题暂时没遇到过了