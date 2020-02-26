---
title: "用shell脚本实现git项目自动（定时）更新"
date: 2020-02-23T09:16:05+08:00
draft: true
---

# 背景

我在学习开源项目的过程中，会囤积不少的开源项目，因此也就需要用一个便捷的脚本进行管理，对开源项目进行自动同步。

# 环境设置

macOS或者Linux都可以，通过shell脚本实现

# 脚本

```

#/bin/bash

Opensource=/Users/hideyoshi/Desktop/OpenSource
#切换到开源项目根目录

cd ${Opensource}

currentpath=$(pwd)

echo "currentpath is :$currentpath"
rm -f /Users/hideyoshi/Desktop/shbin/filelist.txt
ls >>/Users/hideyoshi/Desktop/shbin/filelist.txt

FILENAME=/Users/hideyoshi/Desktop/shbin/filelist.txt

for i in `cat $FILENAME`
do
cd $i
echo "current project is :$i"
git pull
cd ..
done

```

其实没有什么技术难点，用到的也是非常常见的脚本命令。

ls >>  这其实就是将当前目录下所有的目录或者文件，输出到文件中。
之后，再用for循环读取，循环进入目录，循环拉代码即可

当然也有考虑不足的地方，到时候出现了问题，我们再去解决吧！

# 定时执行

其实非常简单，我们只需要借用crontab命令即可。

`crontab -l` 查看目前是否有定时任务。

`crontab -e` 编辑定时任务。

`* 0 * * * /Users/hideyoshi/Desktop/shbin/gitautopull.sh`

如上的命令就是每天0点执行了。


# 结语

其实这本不是什么高深的技术，对许多人来说也是非常基础的技术了。但技术不分高低贵贱，脑海中有什么想法，就想办法去实现。

在这一过程中，我们的工程能力也能得到不小的锻炼，这样也就足够了。