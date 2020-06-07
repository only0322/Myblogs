---
title: "搭建hugo博客的详细方法"
date: 2020-02-20T10:38:41+08:00
draft: true
---
主要步骤如下
1. 安装hugo
2. 配置github
3. 发布

# hugo安装

其实还是非常曲折的过程，原本官方都是推荐用brew install hugo的方式来进行安装，然而国内的网络环境实在是非常不方便。

最好的方式，还是去github慢慢下载。

~~也可以从我的码云下载hugo。~~



> hugo version 
>
可以查看hugo的版本，有显示就正常了。

如果是通过下载可执行程序来安装的hugo，那就得去.bash_profile配置一下环境变量。



# hugo的具体使用

- 建立博客
> hugo new site xxx
xxx为博客名，之后会在当前目录多了xxx的文件夹，里面存放的就是博客的文件。

- 本地启动博客（预览）
> hugo server -t xxx --buildDrafts

其中xxx是主题名

控制台也会现实目前博客的本机地址，可以在浏览器中访问。

- 新建文章

> hugo new post/xxxx.md

在博客的根目录下的content文件夹，会多一个post的文件夹，其中的xxxx.md就是我们的博客markdown文件了。

- 建立github仓库

新建仓库，注意仓库名称是 xxxx(github的用户名).github.io

打如下的命令

> hugo --theme=xxx(主题名) --baseUrl="https:///xxxx.github.io/"  (每次更新都需要，因为是用来生成public文件夹，并且提交的也是上面的东西)

`hugo --theme=m10c --baseUrl="https://only0322.github.io/" --buildDrafts`

之后会在根目录生成public目录。

- 传到github
1. 切换到刚才的public目录
2. 
    `git add .` 

    `git commit -m "xxx"`

    `git remote add origin https://github.com/xxxx/xxxx.github.io.git`    (关联git用)

    `git push -u origin master`  (初次使用才需要-u)


# 博客界面设计


通过修改配置文件，可以修改博客名。
替换图片，则可以修改本主题的头像。
