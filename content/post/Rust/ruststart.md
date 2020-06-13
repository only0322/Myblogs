---
title: "Rust的安装与启动"
date: 2020-06-13T07:02:38+08:00
draft: true
---

## 安装

Rust的安装也分系统

Windows下为:

`https://www.rust-lang.org/tools/install`

MacOS:

`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

## Rust开发环境搭建

Rust官方提供了一个包管理工具，名叫cargo，具体的功能和npm，pip都非常接近。

但要使用cargo的话，项目目录必须是如下的组织：

```toml
project_name
|-  src                     // 放置源文件的目录
    |- main.rs / lib.rs     // 源文件
|-  Cargo.toml              // Cargo的配置文件
```

## 新建项目

`cargo new n_projectname `

后面带上--bin是创建二进制程序，带上--lib是构建自己的库

要注意必须是带下划线的蛇形命名法，否则的话会出现警告，也可以在toml文件里面修改项目名，去掉警告。

## 启动

`cargo run`或者`cargo build`

## cargo的中央托管网站

[https://crates.io/](https://crates.io/)

在网站中搜索我们需要的库，然后会跳转到库的页面，比如提示我们：

`rand = "0.7.3"`

那就说明这个库的版本为0.7.3,我们只需要在cargo.toml里面添加依赖即可。

[dependencies]
rand = "0.7.3"

之后输入cargo build就会进行预编译，同时下载包依赖。

`extern crate rand;` 导入该依赖包


