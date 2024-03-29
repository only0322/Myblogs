---
title: "Jvm原理解析"
date: 2022-01-04T18:40:40+08:00
draft: true
---

jvm知识导图

![知识导图](/images/java/javaknows.png)

## 一、概述

JVM是Java虚拟机的简称，那Jvm到底做了什么事情呢？其实是实现了Java的一次编译，到处运行。

![jvm图](/images/java/javacode.png)

## 二、编译流程

java程序经过一次编译之后，将java代码编译为字节码也就是class文件，然后在不同的操作系统上依靠不同的java虚拟机进行解释，最后再转换为不同平台的机器码，最终得到执行。

这样我们是不是可以推演，如果要在mac系统上运行，是不是只需要安装mac java虚拟机就行了。那么了解了这个基本原理后，我们尝试去做更深的研究，一个普通的java程序它的执行流程到底是怎样的呢？例如我们写了一段这样的代码：

```java
public class HelloWorld { public static void main(String[] args) { System.out.print("Hello world"); } }
```

### 详细步骤

这段程序从编译到运行，最终打印出“Hello world”中间经过了哪些步骤呢？我们直接上图：

![java执行 ](/images/java/javarun.png)

可能通过上面的描述，大家对JVM运行流程有了一个粗略的认识，那么JVM内部到底是怎么执行一个class文件的呢，也就是上图中最后一步第6步的内部细节是怎样的呢？要了解这个问题，我们首先得看一下JVM的内部结构：

![jvm内核](/images/java/jvmcore.png)

从这个结构不难看出，class文件被jvm装载以后，经过jvm的内存空间调配，最终是由执行引擎完成class文件的执行。当然这个过程还有其他角色模块的协助，这些模块协同配合才能让一个java程序成功的运行，它们也是后面学习jvm最重要的部分。



### 内存空间


**JVM内存空间包含：方法区、java堆、java栈、本地方法栈。**

- 方法区是各个线程共享的区域，存放类信息、常量、静态变量。

- java堆也是线程共享的区域，我们的类的实例就放在这个区域，可以想象你的一个系统会产生很多实例，因此java堆的空间也是最大的。如果java堆空间不足了，程序会抛出OutOfMemoryError异常。

- java栈是每个线程私有的区域，它的生命周期与线程相同，一个线程对应一个java栈，每执行一个方法就会往栈中压入一个元素，这个元素叫“栈帧”，而栈帧中包括了方法中的局部变量、用于存放中间状态值的操作栈，这里面有很多细节，我们以后再讲。如果java栈空间不足了，程序会抛出StackOverflowError异常，想一想什么情况下会容易产生这个错误，对，递归，递归如果深度很深，就会执行大量的方法，方法越多java栈的占用空间越大。

- 每个帧代表一个方法，Java方法有两种返回方式，return和抛出异常，两种方式都会导致该方法对应的帧出栈和释放内存。

## 三、垃圾回收

jvm划分出了新生代和老生代两种对象。

![java新生代](/images/java/javanewold.png)

### 1.新生代和老生代

>堆大小 = 新生代 + 老年代。默认下，新生代 ( Young ) = 1/3 的堆空间大小，老年代 ( Old ) = 2/3 的堆空间大小；

>新生代 ( Young ) 被细分为 Eden 和 两个 Survivor 区域，这两个 Survivor 区域分别被命名为 from 和 to，以示区分。默认的，Edem : from : to = 8 : 1 : 1；

>JVM 每次只会使用 Eden 和其中的一块 Survivor 区域来为对象服务，所以无论什么时候，总是有一块 Survivor 区域是空闲着的。因此，新生代实际可用的内存空间为 9/10 ( 即90% )的新生代空间；

>GC 分为两种：老生代中采用标记-整理算法的Full GC ( 或称为 Major GC )和新生代中采用复制算法的Minor GC。新生代是 GC 收集垃圾的频繁区域；

Eden是伊甸园，亚当和夏娃最初居住的地方，jvm应该是想表达这是对象最开始产生的地方。Survivor是幸存者，也就是没有被gc的数据吧。

### 2.GC的模式

young GC:只会收集新生代的GC

old GC:只会收集老生代的GC

混合GC

full GC:整个堆能收集的都会回收

### 3.新生代和老生代的区别

新生代主要存放的是哪些很快就会被GC回收掉的或者不是特别大的对象，老生代会存放一些特别大的对象或者是经历了几次回收仍然存活的对象。

新生代区域特点： 
- 绝大多数新创建的对象都存放在这个区域
- 此区域一般来说较小而且JVM垃圾回收频率较高
- 采用的算法和存放对象特点使得该区域JVM垃圾回收的效率也非常高

老生代区域特点： 
- 将在"新生代"中生存了较长时间的对象转移过来
- 区域一般要大一些而且增长的速度相对于"新生代"要慢一些
- 垃圾回收的执行频率也会低很多


### 4.标记-整理算法

回收的依据是:**设立若干种根对象，当任何一个根对象到某一个对象均不可达时，则认为这个对象是可以被回收的。**从根（GC Roots）的对象作为起始点，开始向下搜索，搜索所走过的路径称为“引用链”，当一个对象到GC Roots没有任何引用链相连（用图论的概念来讲，就是从GC Roots到这个对象不可达）时，则证明此对象是不可用的。



标记-整理算法分为两个阶段：
1. 遍历GC Roots可达的对象，标记为已存活，如果不可达标记为未存活；
2. 将已存活的对象按照顺序排列在内存中，修改新的地址，将末端内存以后的对象清除掉

标记整理的内存使用率更高。

GC时会全局停顿。