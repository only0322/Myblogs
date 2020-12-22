---
title: "趣谈Linux操作系统学习笔记"
date: 2020-10-30T10:38:09+08:00
draft: true
---

## 综述

### 1.命令配合grep组合使用

如：`ps -ef | grep "zsh"` 这样就可以找到名称中含有zsh的进程了。

![psef](/images/geek/linux/psef.png)

## 系统初始化

### 1. 0号进程和1号进程

0号进程不会通过fork和kernel_thread生成 其实是一个进程管理的工具，通过kernel_thread来创建一号进程。

分层的权限机制:

![内核图](/images/geek/linxu/thread0.png)

1号进程本质上则是一个文件，运行各种配置信息：

```cpp
····
  if (ramdisk_execute_command) {
    ret = run_init_process(ramdisk_execute_command);

  }
····
  if (!try_to_run_init_process("/sbin/init") ||
      !try_to_run_init_process("/etc/init") ||
      !try_to_run_init_process("/bin/init") ||
      !try_to_run_init_process("/bin/sh"))
    return 0;
```

### 2.ramdisk 的作用

init 终于从内核到用户态了。一开始到用户态的是 ramdisk 的 init，后来会启动真正根文件系统上的 init，成为所有用户态进程的祖先。

init 程序是在文件系统上的，文件系统一定是在一个存储设备上的，例如硬盘。Linux 访问存储设备，要有驱动才能访问。如果存储系统数目很有限，那驱动可以直接放到内核里面。

ramdisk是为了防止要访问的东西越来越多，对其进行统一管理的**根文件系统**。

运行 ramdisk 上的 /init。等它运行完了就已经在用户态了。/init 这个程序会先根据存储系统的类型加载驱动，有了驱动就可以设置真正的根文件系统了。有了真正的根文件系统，ramdisk 上的 /init 会启动文件系统上的 init。

### 3.2号进程

用来管理内核态的所有进程。

### 4.系统的调用方式

讲述如何打开一个文件等操作。

![调用](/images/geek/linxu/diaoyong.png)