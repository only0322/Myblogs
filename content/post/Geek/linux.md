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

### 5.软连接和硬链接

硬链接是通过索引节点进行的链接。在Linux中，多个文件指向同一个索引节点是允许的，像这样的链接就是硬链接。硬链接只能在同一文件系统中的文件之间进行链接，不能对目录进行创建。

如果删除硬链接对应的源文件，则硬链接文件仍然存在，而且保存了原有的内容，这样可以起到防止因为误操作而错误删除文件的作用。由于硬链接是有着相同 inode 号仅文件名不同的文件，因此，删除一个硬链接文件并不影响其他有相同 inode 号的文件。

```coffee
link oldfile newfile 
ln oldfile newfile
```

软链接（也叫符号链接）与硬链接不同，文件用户数据块中存放的内容是另一文件的路径名的指向。软链接就是一个普通文件，只是数据块内容有点特殊。软链接可对文件或目录创建。

软链接主要应用于以下两个方面：一是方便管理，例如可以把一个复杂路径下的文件链接到一个简单路径下方便用户访问；另一方面就是解决文件系统磁盘空间不足的情况。

例如某个文件文件系统空间已经用完了，但是现在必须在该文件系统下创建一个新的目录并存储大量的文件，那么可以把另一个剩余空间较多的文件系统中的目录链接到该文件系统中，这样就可以很好的解决空间不足问题。

删除软链接并不影响被指向的文件，但若被指向的原文件被删除，则相关软连接就变成了死链接。

```coffee
ln -s old.file soft.link
ln -s old.dir soft.link.dir
```

