---
title: "Rust的进阶知识"
date: 2020-06-13T13:17:38+08:00
draft: true
---

## 一、Rust 组织管理

Rust 中有三个重要的组织概念：箱、包、模块。

### 1.箱

"箱"是二进制程序文件或者库文件，存在于"包"中。

"箱"是树状结构的，它的树根是编译器开始运行时编译的源文件所编译的程序。

### 2.包

当我们使用 Cargo 执行 new 命令创建 Rust 工程时，工程目录下会建立一个 Cargo.toml 文件。工程的实质就是一个包，包必须由一个 Cargo.toml 文件来管理，该文件描述了包的基本信息以及依赖项。

一个包最多包含一个库"箱"，可以包含任意数量的二进制"箱"，但是至少包含一个"箱"（不管是库还是二进制"箱"）。

当使用 cargo new 命令创建完包之后，src 目录下会生成一个 main.rs 源文件，Cargo 默认这个文件为二进制箱的根，编译之后的二进制箱将与包名相同。

### 3.模块

```rust
mod nation {
    mod government {
        fn govern() {}
    }
    mod congress {
        fn legislate() {}
    }
    mod court {
        fn judicial() {}
    }
}
```

在rust中，主要通过mod关键字来创建和管理模块。

### 4.模块的访问权限

Rust 中有两种简单的访问权：公共（public）和私有（private）。

默认情况下，如果不加修饰符，模块中的成员访问权将是私有的。
如果想使用公共权限，需要使用 pub 关键字。

对于私有的模块，只有在与其平级的位置或下级的位置才能访问，不能从其外部访问。

```rust
mod nation {
    pub mod government {
        pub fn govern() {}
    }

    mod congress {
        pub fn legislate() {}
    }
    
    mod court {
        fn judicial() {
            super::congress::legislate();
        }
    }
}

fn main() {
    nation::government::govern();
}
```

### 5.use关键字

```rust
mod nation {
    pub mod government {
        pub fn govern() {}
    }
}

use crate::nation::government::govern;

fn main() {
    govern();
}
```

## 二、错误处理

### 1.panic宏 不可恢复的错误

```rust
fn main() {
    panic!("error occured");
    println!("Hello, Rust");
}
```

这样程序就无法输出hello，而会在panic宏的地方结束运行。

不可恢复的错误一定会导致程序受到致命的打击而终止运行。

### 2.可恢复的错误

比如如下的文件打开程序
```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");
    match f {
        Ok(file) => {
            println!("File opened successfully.");
        },
        Err(err) => {
            println!("Failed to open the file.");
        }
    }
}
```
如果 hello.txt 文件不存在，会打印 "Failed to open the file."。

### 3.错误传递

如果想使一个可恢复错误按不可恢复错误处理，Result 类提供了两个办法：unwrap() 和 expect(message: &str) ：

```rust
use std::fs::File;

fn main() {
    let f1 = File::open("hello.txt").unwrap();
    let f2 = File::open("hello.txt").expect("Failed to open.");
}
```

### 4.判断错误类型

主要用到kind方法：

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_text_from_file(path: &str) -> Result<String, io::Error> {
    let mut f = File::open(path)?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}

fn main() {
    let str_file = read_text_from_file("hello.txt");
    match str_file {
        Ok(s) => println!("{}", s),
        Err(e) => {
            match e.kind() {
                io::ErrorKind::NotFound => {
                    println!("No such file");
                },
                _ => {
                    println!("Cannot read the file");
                }
            }
        }
    }
}
```

这个时候回输出No such file而不是后一种错误。

## 三、泛型

比如如下的一段代码:
```rust
struct Point<T> {
    x: T,
    y: T
}
```

可以有多种使用方式:

```rust
let p1 = Point {x: 1, y: 2};
let p2 = Point {x: 1.0, y: 2.0};
```

## 四、Rust的生命周期

### 1.初步了解

Rust 生命周期机制是与所有权机制同等重要的资源管理机制。

之所以引入这个概念主要是应对复杂类型系统中资源管理的问题。

引用是对待复杂类型时必不可少的机制，毕竟复杂类型的数据不能被处理器轻易地复制和计算。

但引用往往导致极其复杂的资源管理问题，首先认识一下垂悬引用：

```rust
{
    let r;

    {
        let x = 5;
        r = &x;
    }

    println!("r: {}", r);
}
```
这段代码是不会通过 Rust 编译器的，原因是 r 所引用的值已经在使用之前被释放。

### 2.生命周期注释

生命周期注释是描述引用生命周期的办法。

虽然这样并不能够改变引用的生命周期，但可以在合适的地方生命两个引用的生命周期一致。

生命收起注释用单引号开头，跟着一个小写字母单词：

```rust
&i32        // 常规引用
&'a i32     // 含有生命周期注释的引用
&'a mut i32 // 可变型含有生命周期注释的引用
```

## 四、文件与IO

### 1.接受命令行参数

命令行程序是计算机程序最基础的存在形式，几乎所有的操作系统都支持命令行程序并将可视化程序的运行基于命令行机制。

命令行程序必须能够接收来自命令行环境的参数，这些参数往往在一条命令行的命令之后以空格符分隔。

在很多语言中（如 Java 和 C/C++）环境参数是以主函数的参数（常常是一个字符串数组）传递给程序的，但在 Rust 中主函数是个无参函数，环境参数需要开发者通过 std::env 模块取出，过程十分简单：


```rust
fn main() {
    let args = std::env::args();
    println!("{:?}", args);
}
```

### 2.命令行输入

```rust
use std::io::stdin;

fn main() {
let mut str_buf = String::new();

    stdin().read_line(&mut str_buf)
        .expect("Failed to read line.");

    println!("Your input line is \n{}", str_buf);
}
```

### 3.文件读取

use std::fs;

```rust
fn main() {
    let text = fs::read_to_string("D:\\text.txt").unwrap();
    println!("{}", text);
}
```

### 4.文件写入

文件写入分为一次性写入和流式写入。流式写入需要打开文件，打开方式有"新建"（create）和"追加"（append）两种。

一次性写入：
```rust
use std::fs;

fn main() {
    fs::write("D:\\text.txt", "FROM RUST PROGRAM")
        .unwrap();
}
```


如果想使用流的方式写入文件内容，可以使用 std::fs::File 的 create 方法：


```rust
use std::io::prelude::*;
use std::fs::File;

fn main() {
    let mut file = File::create("D:\\text.txt").unwrap();
    file.write(b"FROM RUST PROGRAM").unwrap();
}
```

## 五、集合与字符串

集合（Collection）是数据结构中最普遍的数据存放形式，Rust 标准库中提供了丰富的集合类型帮助开发者处理数据结构的操作。

### 1.向量

向量（Vector）是一个存放多值的单数据结构，该结构将相同类型的值线性的存放在内存中。

向量是线性表，在 Rust 中的表示是 Vec<T>。

向量的使用方式类似于列表（List），我们可以通过这种方式创建指定类型的向量：

```rust
let vector: Vec<i32> = Vec::new(); // 创建类型为 i32 的空向量
let vector = vec![1, 2, 4, 8];     // 通过数组创建向量
```
向量只有 push 方法来追加单个元素：

```rust
fn main() {
    let mut vector = vec![1, 2, 4, 8];
    vector.push(16);
    vector.push(32);
    vector.push(64);
    println!("{:?}", vector);
}
```

append 方法用于将一个向量拼接到另一个向量的尾部：


```rust
fn main() {
    let mut v1: Vec<i32> = vec![1, 2, 4, 8];
    let mut v2: Vec<i32> = vec![16, 32, 64];
    v1.append(&mut v2);
    println!("{:?}", v1);
}
```

get 方法用于取出向量中的值：

```rust

fn main() {
    let mut v = vec![1, 2, 4, 8];
    println!("{}", match v.get(0) {
        Some(value) => value.to_string(),
        None => "None".to_string()
    });
}
```

其他的，百度找吧。

### 2.字符串

新建字符串：

`let string = String::new();`

基础类型转换成字符串：

```rust
let one = 1.to_string();         // 整数到字符串
let float = 1.3.to_string();     // 浮点数到字符串
let slice = "slice".to_string(); // 字符串切片到字符串
```

字符串追加：

```rust
let mut s = String::from("run");
s.push_str("oob"); // 追加字符串切片
s.push('!');       // 追加字符
```

还有+号拼接字符串，len()方法，format方法等等。

### 3.哈希表

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();

    map.insert("color", "red");
    map.insert("size", "10 m^2");

    println!("{}", map.get("color").unwrap());
}
```

insert 方法和 get 方法是映射表最常用的两个方法。

映射表支持迭代器：

```rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();

    map.insert("color", "red");
    map.insert("size", "10 m^2");

    for p in map.iter() {
        println!("{:?}", p);
    }
}

```

## 六、面向对象

### 1.封装

封装就是对外显示的策略，在 Rust 中可以通过模块的机制来实现最外层的封装，并且每一个 Rust 文件都可以看作一个模块，模块内的元素可以通过 pub 关键字对外明示。这一点在"组织管理"章节详细叙述过。

"类"往往是面向对象的编程语言中常用到的概念。"类"封装的是数据，是对同一类数据实体以及其处理方法的抽象。在 Rust 中，我们可以使用结构体或枚举类来实现类的功能：

```rust
pub struct ClassName {
    pub field: Type,
}

pub impl ClassName {
    fn some_method(&self) {
        // 方法函数体
    }
}

pub enum EnumName {
    A,
    B,
}

pub impl EnumName {
    fn some_method(&self) {

    } 
}
```

完整的类：

```rust
second.rs
pub struct ClassName {
    field: i32,
}

impl ClassName {
    pub fn new(value: i32) -> ClassName {
        ClassName {
            field: value
        }
    }

    pub fn public_method(&self) {
        println!("from public method");
        self.private_method();
    }

    fn private_method(&self) {
        println!("from private method");
    }
}
main.rs
mod second;
use second::ClassName;

fn main() {
    let object = ClassName::new(1024);
    object.public_method();
}
```

但Rust并不支持继承。

## 七、并发编程

### 1.线程

线程（thread）是一个程序中独立运行的一个部分。
线程不同于进程（process）的地方是线程是程序以内的概念，程序往往是在一个进程中执行的。

在有操作系统的环境中进程往往被交替地调度得以执行，线程则在进程以内由程序进行调度。

由于线程并发很有可能出现并行的情况，所以在并行中可能遇到的死锁、延宕错误常出现于含有并发机制的程序。

为了解决这些问题，很多其它语言（如 Java、C#）采用特殊的运行时（runtime）软件来协调资源，但这样无疑极大地降低了程序的执行效率。

C/C++ 语言在操作系统的最底层也支持多线程，且语言本身以及其编译器不具备侦察和避免并行错误的能力，这对于开发者来说压力很大，开发者需要花费大量的精力避免发生错误。

Rust 不依靠运行时环境，这一点像 C/C++ 一样。

但 Rust 在语言本身就设计了包括所有权机制在内的手段来尽可能地把最常见的错误消灭在编译阶段，这一点其他语言不具备。

Rust 中通过 std::thread::spawn 函数创建新进程：

```rust
use std::thread;
use std::time::Duration;

fn spawn_function() {
    for i in 0..5 {
        println!("spawned thread print {}", i);
        thread::sleep(Duration::from_millis(1));
    }
}

fn main() {
    thread::spawn(spawn_function);

    for i in 0..3 {
        println!("main thread print {}", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

std::thread::spawn 函数的参数是一个无参函数，但上述写法不是推荐的写法，我们可以使用闭包（closures）来传递函数作为参数：

```rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 0..5 {
            println!("spawned thread print {}", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 0..3 {
        println!("main thread print {}", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

Join方法
```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 0..5 {
            println!("spawned thread print {}", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 0..3 {
        println!("main thread print {}", i);
        thread::sleep(Duration::from_millis(1));
    }

    handle.join().unwrap();
}
```


消息传递

Rust 中一个实现消息传递并发的主要工具是通道（channel），通道有两部分组成，一个发送者（transmitter）和一个接收者（receiver）。

std::sync::mpsc 包含了消息传递的方法：

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });

    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```



## 总结

有需要再补充吧。