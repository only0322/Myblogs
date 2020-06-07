---
title: "如何在github高效地搜索开源项目"
date: 2020-02-23T15:55:50+08:00
draft: true
---


# 概述

本文讲述如何在github的搜索栏找到自己想要的项目。

重点无非如下：

- 项目名

- 项目概述

- star数

- 提交时间（活跃度）

- 编程语言

# 用法

1. 项目名

    `in:name xxx`

    通过如下命令，可以搜索名字里带xxx的项目。

2. star数

    `stars:>xxx` 

    可以搜索star数大于xxx的项目。

3. fork数

    `forks:>xxx`

    同理

4. 搜readme

    `in:readme xxx`

    可以在项目的readme文件里，搜索包含某字符的项目，一般可以用来搜某些用到本技术的项目。

5. 搜描述，概述

    `in:description xxx`

    对应项目名下方的项目概述

6. 搜语言

    `language:xxx`

7. 搜更新时间

    `pushed:>xxxx-xx-xx`


#  总结，举例

比如我想搜用C++写的爬虫软件，并且具有一定的火热程度。

使用如下的条件:

in:description 爬虫 language:cpp stars:>1000

好吧，搜不到。但是把语言改成Python，就能找到大约26个项目，证明语句是可以正常工作的。

