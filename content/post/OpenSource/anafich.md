---
title: "Anafich——报文解析工具"
date: 2020-04-29T21:22:23+08:00
draft: true
---

#### 项目背景

通过预先配置好报文或文件的格式，在项目中遇到需要人工解析的报文，可以通过AnaFich来实现。

#### 源码

[AnaFich](https://gitee.com/onlyyyy/AnaFich)

#### 用到的编程语言和环境

操作系统：Windows10/MacOS

语言：C++/Qt

#### 软件流程

##### 1.创建项目

通过读取和写入Json文件，完成对项目的配置。之后每次使用软件都需要读取Json文件，获得目前的项目和报文格式的配置。

Json文件格式示例：

```Json
{
    "project": [
        {
            "description": "2020",
            "file": [
                {
                    "detail": [
                        {
                            "description": "1",
                            "length": "4",
                            "name": "名称",
                            "type": "number"
                        },
                        {
                            "description": "",
                            "length": "8",
                            "name": "日期",
                            "type": "number"
                        },
                        {
                            "description": "",
                            "length": "6",
                            "name": "时间",
                            "type": "number"
                        },
                        {
                            "description": "",
                            "length": "8",
                            "name": "ID",
                            "type": "string"
                        },
                        {
                            "description": "",
                            "length": "1",
                            "name": "标志",
                            "type": "number"
                        },
                        {
                            "description": "",
                            "length": "32",
                            "name": "保留",
                            "type": "number"
                        },
                        {
                            "description": "",
                            "length": "64",
                            "name": "ii",
                            "type": "number"
                        }
                    ],
                    "filename": "2000"
                }
            ],
            "name": "测试"
        }
    ]
}

```

##### 2.项目文件配置

通过QJson库对Json文件进行读取和增删改，在界面上可以配置报文每个域的长度和数据类型，与数据库建表的操作类似。

![配置](/images/anafich/set.png)

##### 3.文件与报文的解析

将文件和报文读取到QString中，然后根据回车换行符进行分隔。

```Qt
QStringList fileList = QString(fileText).split(QRegExp("[\r\n]"),QString::SkipEmptyParts);
```

通过正则表达式来实现。

同时程序会按配置读取文件或报文的内容，将结果显示在界面上。

![截图](/images/anafich/fileslove.png)

##### 4.其他

剩下的其实都是Qt的界面设计，TableView、TreeView之类的控件使用。


#### 总结

做完这个项目之后，学到了很多东西，也能在工作中利用AnaFiche来节约很多时间了。




