---
title: "Rust基础语法"
date: 2020-06-13T07:38:43+08:00
draft: true
---

## 一、变量

### 1. 可变与不可变


Rust的变量分可变变量和不可变变量。

`let a = 1`就是一个不可变变量。这一点对于写过C的程序员来说很难理解。但Rust此举其实是为了防止程序中的变量以一种不可控的方式更改了自身的值。

`let mut a = 1`,此时a就是一个可变变量了。

但不可变变量是可以重新绑定数值的，比如如下写法

```rust
let a = 1;
let a = 2;
```

是合法的。


### 2. 重影


重影就是指变量的名称可以被重新使用的机制：

```rust
fn main() {
    let x = 5;
    let x = x + 1;
    let x = x * 2;
    println!("The value of x is: {}", x);
}
```

重影与可变变量的赋值不是一个概念，重影是指用同一个名字重新代表另一个变量实体，其类型、可变属性和值都可以变化。但可变变量赋值仅能发生值的变化。

```rust
let mut s = "123";
s = s.len();
```

这段程序就会出错，因为字符串变量不能被赋整形。

## 二、输出

### 1. 最简单的输出

```rust
fn main() { 
    let a = 12; 
    println!("a is {}", a); 
}
```

在 {} 之间可以放一个数字，它将把之后的可变参数当作一个数组来访问，下标从 0 开始。

`println!("a is {0}, a again is {0}", a); `


转义符

```rust
fn main() { 
    println!("{{}}"); 
} 
```
输出结果是{}

## 三、注释

用//，没有什么特别的。

## 四、函数

###  1.常规用法

```rust
fn main() {
    println!("Hello, world!");
    another_function();
}

fn another_function() {
    println!("Hello, runoob!");
}
```
rust的函数并不一定要写在主函数的前面，任意位置都可以。

函数名建议用蛇形。

### 2.函数参数

Rust 中定义函数如果需要具备参数必须声明参数名称和类型

```rust
fn main() {
    another_function(5, 6);
}

fn another_function(x: i32, y: i32) {
    println!("x 的值为 : {}", x);
    println!("y 的值为 : {}", y);
}
```

### 3.函数体语句表达式

```ruby
fn main() {
    let x = 5;

    let y = {
        let x = 3;
        x + 1
    };

    println!("x 的值为 : {}", x);
    println!("y 的值为 : {}", y);
}
```

和JS的语法有点类似，此时变量y的赋值，表达式是必须要返回值的，比如删掉x+1就会报错。

### 4.函数返回值

```rust
fn add(a: i32, b: i32) -> i32 {
    return a + b;
}
```

但是 Rust 不支持自动返回值类型判断！如果没有明确声明函数返回值的类型，函数将被认为是"纯过程"，不允许产生返回值，return 后面不能有返回值表达式。

## 五、条件语句 if

### 1.if的基本用法

if语句不需要小括号，但不是不允许。

```rust
fn main() { 
    let a = 12; 
    let b; 
    if a > 0 { 
        b = 1; 
    }  
    else if a < 0 { 
        b = -1; 
    }  
    else { 
        b = 0; 
    }; 
    println!("b is {}", b); 
} 
```

### 2.与C语言的区别

条件表达式必须是布尔类型的，不会出现C语言中if(number=0)这种难以察觉的错误了。

比如下列代码就不被允许：

```rust
fn main() { 
    let number = 3; 
    if number {   // 报错，expected `bool`, found integerrustc(E0308)
        println!("Yes");
    } 
} 
```

但if表达式支持函数式，三目运算符。

```rust
fn main() { 
    let a = 3; 
    let number = if a > 0 { 1 } else { -1 }; 
    println!("number 为 {}", number); 
}
```

## 六、循环

### 1.while

```rust
fn main() {
    let mut number = 1; 
    while number != 4 { 
        println!("{}", number); 
        number += 1; 
    } 
    println!("EXIT"); 
} 
```
### 2.for

rust不支持`for(int i=0;i<10;i++)`这种三元语句，只能用如下的方式：

```rust
fn main() { 
    let a = [10, 20, 30, 40, 50]; 
    for i in a.iter() { 
        println!("值为 : {}", i); 
    } 
} 
```
或者通过下标访问:

```rust
fn main() { 
let a = [10, 20, 30, 40, 50]; 
    for i in 0..5 { 
        println!("a[{}] = {}", i, a[i]); 
    } 
} 
```

### 3.无限循环

一般用于在循环的开始处，无法预知要循环多少次的情况下，使用loop。

```
fn main() { 
    let s = ['R', 'U', 'N', 'O', 'O', 'B']; 
    let mut i = 0; 
    loop { 
        let ch = s[i]; 
        if ch == 'O' { 
            break; 
        } 
        println!("\'{}\'", ch);
        i += 1; 
    } 
}
```

## 七、所有权，资源管理

### 1.所有权概念

所有权有以下三条规则：

1.Rust 中的每个值都有一个变量，称为其所有者。

2.一次只能有一个所有者。

3.当所有者不在程序运行范围时，该值将被删除。

这三条规则是所有权概念的基础。

例如：
```rust
{
    // 在声明以前，变量 s 无效
    let s = "runoob";
    // 这里是变量 s 的可用范围
}
// 变量范围已经结束，变量 s 无效
```

### 2.内存分配

rust会在合适的地方自动添加一个释放资源的函数调用，而不需要程序员自己去释放资源。

这种简单的机制，解决了一个令程序员头疼的编程问题。

### 3.变量数据交互

多个变量可以在 Rust 中以不同的方式与相同的数据交互：

比如
```rust
let x = 5;
let y = x;
```

但如果是堆的对象就有比较特殊的情况了：

```rust
let s1 = String::from("hello");
let s2 = s1; 
println!("{}, world!", s1); // 错误！s1 已经失效
```

两个 String 对象在栈中，每个 String 对象都有一个指针指向堆中的 "hello" 字符串。在给 s2 赋值时，只有栈中的数据被复制了，堆中的字符串依然还是原来的字符串。

当变量超出范围时，Rust 自动调用释放资源函数并清理该变量的堆内存。但是 s1 和 s2 都被释放的话堆区中的 "hello" 被释放两次，这是不被系统允许的。为了确保安全，在给 s2 赋值时 s1 已经无效了。

可以通过克隆的方式继续使用：

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1.clone();
    println!("s1 = {}, s2 = {}", s1, s2);
}
```

对于函数而言，上述说法依然有效，就是情况更为复杂

```rust
fn main() {
    let s = String::from("hello");
    // s 被声明有效

    takes_ownership(s);
    // s 的值被当作参数传入函数
    // 所以可以当作 s 已经被移动，从这里开始已经无效

    let x = 5;
    // x 被声明有效

    makes_copy(x);
    // x 的值被当作参数传入函数
    // 但 x 是基本类型，依然有效
    // 在这里依然可以使用 x 却不能使用 s

} // 函数结束, x 无效, 然后是 s. 但 s 已被移动, 所以不用被释放


fn takes_ownership(some_string: String) { 
    // 一个 String 参数 some_string 传入，有效
    println!("{}", some_string);
} // 函数结束, 参数 some_string 在这里释放

fn makes_copy(some_integer: i32) { 
    // 一个 i32 参数 some_integer 传入，有效
    println!("{}", some_integer);
} // 函数结束, 参数 some_integer 是基本类型, 无需释放
```

函数的返回值：

```rust
fn main() {
    let s1 = gives_ownership();
    // gives_ownership 移动它的返回值到 s1

    let s2 = String::from("hello");
    // s2 被声明有效

    let s3 = takes_and_gives_back(s2);
    // s2 被当作参数移动, s3 获得返回值所有权
} // s3 无效被释放, s2 被移动, s1 无效被释放.

fn gives_ownership() -> String {
    let some_string = String::from("hello");
    // some_string 被声明有效

    return some_string;
    // some_string 被当作返回值移动出函数
}

fn takes_and_gives_back(a_string: String) -> String { 
    // a_string 被声明有效

    a_string  // a_string 被当作返回值移出函数
}
```

### 4.引用与租借

- 引用
```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = &s1;
    println!("s1 is {}, s2 is {}", s1, s2);
}
```
可以看做是指针。

- 租借

引用只能租借值的所有权，在所有权变更后就会失效。

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = &s1;
    let s3 = s1;
    println!("{}", s2);
}
```

这段程序不正确：因为 s2 租借的 s1 已经将所有权移动到 s3，所以 s2 将无法继续租借使用 s1 的所有权。如果需要使用 s2 使用该值，必须重新租借：

```rust
fn main() {
    let s1 = String::from("hello");
    let mut s2 = &s1;
    let s3 = s2;
    s2 = &s3; // 重新从 s3 租借所有权
    println!("{}", s2);
}
```

## 八、切片

```rust
fn main() {
    let s = String::from("broadcast");

    let part1 = &s[0..5];
    let part2 = &s[5..9];

    println!("{}={}+{}", s, part1, part2);
}
```

但切片的数据不能更改值:

```rust
fn main() {
    let mut s = String::from("runoob");
    let slice = &s[0..3];
    s.push_str("yes!"); // 错误
    println!("slice = {}", slice);
}
```
## 九、str与String类型

在 Rust 中有两种常用的字符串类型：str 和 String。str 是 Rust 核心语言类型，就是本章一直在讲的字符串切片（String Slice），常常以引用的形式出现（&str）

。
凡是用双引号包括的字符串常量整体的类型性质都是 &str：

String 类型是 Rust 标准公共库提供的一种数据类型，它的功能更完善——它支持字符串的追加、清空等实用的操作。String 和 str 除了同样拥有一个字符开始位置属性和一个字符串长度属性以外还有一个容量（capacity）属性。

String 和 str 都支持切片，切片的结果是 &str 类型的数据。

```rust
fn main() {
    let arr = [1, 3, 5, 7, 9];
    let part = &arr[0..3];
    for i in part.iter() {
        println!("{}", i);
    }
}
```
 
## 十、结构体与元组

### 1.结构体

Rust 中的结构体（Struct）与元组（Tuple）都可以将若干个类型不一定相同的数据捆绑在一起形成整体，但结构体的每个成员和其本身都有一个名字，这样访问它成员的时候就不用记住下标了。

元组常用于非定义的多值传递，而结构体用于规范常用的数据结构。结构体的每个成员叫做"字段"。

```rust
struct Site {
    domain: String,
    name: String,
    nation: String,
    found: u32
}
```

```rust
let site = Site {
    domain: String::from("www.example.com"),
    name: String::from("EXAMPLE"),
    ..runoob
};
```

### 2.元组结构体

有一种更简单的定义和使用结构体的方式：元组结构体。

元组结构体是一种形式是元组的结构体。

与元组的区别是它有名字和固定的类型格式。它存在的意义是为了处理那些需要定义类型（经常使用）又不想太复杂的简单数据：

```rust
fn main() {
    struct Color(u8, u8, u8);
    struct Point(f64, f64);

    let black = Color(0, 0, 0);
    let origin = Point(0.0, 0.0);

    println!("black = ({}, {}, {})", black.0, black.1, black.2);
    println!("origin = ({}, {})", origin.0, origin.1);
}
```

### 3.结构体方法

方法（Method）和函数（Function）类似，只不过它是用来操作结构体实例的。

如果你学习过一些面向对象的语言，那你一定很清楚函数一般放在类定义里并在函数中用 this 表示所操作的实例。

Rust 语言不是面向对象的，从它所有权机制的创新可以看出这一点。但是面向对象的珍贵思想可以在 Rust 实现。

结构体方法的第一个参数必须是 &self，不需声明类型，因为 self 不是一种风格而是关键字。

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn wider(&self, rect: &Rectangle) -> bool {
        self.width > rect.width
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle { width: 40, height: 20 };

    println!("{}", rect1.wider(&rect2));
}
```

调用结构体方法的时候，不需要写self，是出于方便性的考虑。

### 4.结构体关联函数

之所以"结构体方法"不叫"结构体函数"是因为"函数"这个名字留给了这种函数：它在 impl 块中却没有 &self 参数。
这种函数不依赖实例，但是使用它需要声明是在哪个 impl 块中的。
一直使用的 `String::from` 函数就是一个"关联函数"。

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn create(width: u32, height: u32) -> Rectangle {
        Rectangle { width, height }
    }
}

fn main() {
    let rect = Rectangle::create(30, 50);
    println!("{:?}", rect);
}
```

## 十一、枚举类


```rust
#[derive(Debug)]

enum Book {
    Papery, Electronic
}

fn main() {
    let book = Book::Papery;
    println!("{:?}", book);
}

```

枚举类没有C++那么方便，但也不是很难用。在print函数里面用:?，需要引入debug模块。








