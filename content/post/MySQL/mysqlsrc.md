---
title: "MySQL的代码结构"
date: 2020-06-03T19:46:59+08:00
draft: true
---

### 代码结构简介

在阅读源码之前，我们首先要熟悉MySQL的项目结构。而MySQL由于项目过于庞大，开发人员众多，理解每个文件夹、模块的用途，也是一件非常重要的事。

以下是MySQL的项目路径：

- BUILD: 内含在各个平台、各种编译器下进行编译的脚本。如compile-pentium-debug表示在pentium架构上进行编译的脚本。

- Client: 客户端工具，如mysql, mysqladmin之类。

- Cmd-line-utils: readline, libedit工具。

- Config: 给aclocal使用的配置文件。

- Dbug: 提供一些调试用的宏定义。

- Extra: 提供innochecksum，resolveip等额外的小工具。

- Include: 包含的头文件

- Libmysql: 库文件，生产libmysqlclient.so。

- Libmysql_r: 线程安全的库文件，生成libmysqlclient_r.so。

- Libservices: 5.5.0中新加的目录，实现了打印功能。

- Man: 手册页。

- Mysql-test: mysqld的测试工具一套。

- Mysys: 为跨平台计，MySQL自己实现了一套常用的数据结构和算法，如string, hash等。

- Netware: 在netware平台上进行编译时需要的工具和库。

- Plugin: mysql以插件形式实现的部分功能。

- Pstack: 异步栈追踪工具。

- Regex: 正则表达式工具。

- Scripts: 提供脚本工具，如mysql_install_db等

- Sql: mysql主要代码，将会生成mysqld文件。

- Sql-bench: 一些评测代码。

- Sql-common: 存放部分服务器端和客户端都会用到的代码。

- Storage: 存储引擎所在目录，如myisam, innodb, ndb等。

- Strings: string库。

- Support-files: my.cnf示例配置文件。

- Tests: 测试文件所在目录。

- Unittest: 单元测试。

- Vio: virtual io系统，是对network io的封装。

- Win: 给windows平台提供的编译环境。

- Zip: zip库工具

