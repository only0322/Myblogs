---
title: "macOS配置环境变量与Linux的不同点"
date: 2020-02-22T21:44:19+08:00
draft: true
---

# 前因后果

在Linux中，用户的环境变量由根目录的.bash_profile管理。事实上macOS也该是如此的，但我这么设置了之后，却发现每次都需要重新source，环境变量才能正常工作。

# 原因

其实也很简单，在同级目录下的.zshrc，是新版macOS更换bash到zsh之后，真正的配置文件。

因此，每次用户登录的时候，调用的不是.bash_profile，而是.zshrc了。

# 解决方案

可以考虑在.zshrc里source我们的配置文件，这样与在Linux的习惯一致。当然也可以直接在.zshrc编写环境变量。

问题到这里，也算是解决了。


