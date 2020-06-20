---
title: "Rust编写的Web服务"
date: 2020-06-13T16:24:37+08:00
draft: true
---

## 一、最基本的Web服务器

先看代码：

```rust
use std::net::{TcpListener, TcpStream};
fn handle_client(_stream: TcpStream) {
    let mut buffer = [0; 512];
    stream.read(&mut buffer).unwrap();
    println!("Request: {}", String::from_utf8_lossy(&buffer[..]));
}
fn main() -> std::io::Result<()> {
    let listener = TcpListener::bind("127.0.0.1:80")?;
    for stream in listener.incoming() {
        handle_client(stream?);
    }
    Ok(())
}
```


## 二、返回应答

修改之前的函数，代码如下：
```ruby
use std::net::{TcpListener,TcpStream};
use std::io::Write;
use std::io::Read;
fn handle_client(mut _stream: TcpStream) {
    let mut buffer = [0; 512];
    _stream.read(&mut buffer).unwrap();
    let response = "HTTP/1.1 200 OK\r\n\r\n"; //返回一个响应行
    _stream.write(response.as_bytes()).unwrap();
    _stream.flush().unwrap();
}
```





