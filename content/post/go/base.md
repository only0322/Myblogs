---
title: "Go语言基础"
date: 2020-09-08T06:12:45+08:00
draft: true
---

### 一、Hello World

```go
package main

import "fmt"

func main() {
   fmt.Println("Hello, World!");
   var a string;
   a = "10"
   fmt.Println(a);
}
```

每个程序都需要main模块和main函数，且函数后的大括号不能另起一行。

### 二、变量

```go
var a int;
fmt.Println(a);// 0 
```
未初始化的变量，输出的值为0，不像C++是随机值。

```go
a := 20;
fmt.Println(a);
```

简写，自动识别变量的类型。

```go
package main

import "unsafe"
const (
    a = "abc"
    b = len(a)
    c = unsafe.Sizeof(a)
)

func main(){
    println(a, b, c)
}
```

常量可以用len(), cap(), unsafe.Sizeof()函数计算表达式的值。常量表达式中，函数必须是内置函数，否则编译不过。

```go
var b = [5]float32{1,2,3,4,5};
fmt.Println(b);
```

数组的实际使用。


```go
package main

import "fmt"
type hello struct {
	a int;
	b string;
	c string;
}
func main() {
	fmt.Println(hello{ a:1,b:"2",c:"3"});
	var Hello hello;
	//hello{1,"2","3"};
	fmt.Println(Hello.a);
}

```
结构体使用，和C语言差不多。



```go
var h map[string]string;
h = make(map[string]string);
h["france"] = "123";
fmt.Println(h);
```

map的基本用法。


```go
package main

import (
    "fmt"
)

type Phone interface {
    call()
}

type NokiaPhone struct {
}

func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()

}
```


golang的接口。


```go
package main

import (
        "fmt"
        "time"
)

func say(s string) {
        for i := 0; i < 5; i++ {
                time.Sleep(100 * time.Millisecond)
                fmt.Println(s)
        }
}

func main() {
        go say("world")
        say("hello")
}
```

go语言并发。


```go
package main

import "fmt"

func sum(s []int, c chan int) {
        sum := 0
        for _, v := range s {
                sum += v
        }
        c <- sum // 把 sum 发送到通道 c
}

func main() {
        s := []int{7, 2, 8, -9, 4, 0}

        c := make(chan int)
        go sum(s[:len(s)/2], c)
        go sum(s[len(s)/2:], c)
        x, y := <-c, <-c // 从通道 c 中接收

        fmt.Println(x, y, x+y)
}
```

go语言的通道。


