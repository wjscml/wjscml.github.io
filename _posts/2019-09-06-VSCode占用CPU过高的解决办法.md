---
layout:     post
title:      VSCode占用CPU过高
subtitle:   vscode启动后占用cup100%的解决
date:       2019-09-06
author:     Aisopos
header-img: img/post-bg-rwd.jpg
catalog: true
tags:
    - 前端
    - 工具
---

VSCode每次打开项目都会在任务管理器产生2个rg.exe进程。CPU会直接超频大红，任务管理器CPU占用率达到100%。

# 解决方案

在VScode中文件->首选项->设置

搜索"search.followSymlinks":true，然后设置为false就可以解决问题了。
    
![Image text](https://github.com/wjscml/wjscml.github.io/blob/master/img/vscode-setting.png?raw=true)