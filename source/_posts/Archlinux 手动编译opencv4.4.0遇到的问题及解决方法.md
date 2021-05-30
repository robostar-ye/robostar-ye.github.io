---
title: Archlinux 手动编译opencv4.4.0遇到的问题及解决方法
date: 2021-05-31 0:0:0
tags: 
- opencv4
- pytorch
- archlinux
categories: 
- 开发环境配置
description: 重新构建我的博客的流程，留下作为参考
---

## 1. gcc版本问题

​	由于archlinx的版本更新过快，cuda以及opencv一些内容还不支持gcc10，所以在cmake前需要手动指定gcc版本，在这里我用的是gcc8。

另外，推荐使用下面这条指令，可以同时安装cudnn与cuda，并且顺带会安装上其依赖项gcc8，解决系统自带gcc版本过高的问题。

```bash
yay -S cudnn7-cuda10.1
```



## 2. 自编译opencv与arch源的opencv冲突问题

​	archlinux提供了一个python-pytorch-opt-cuda-1.8.1-6包，可以滚动更新使用最新的pytorch，但是它依赖于arch源提供的opencv。我自己编译时选择了generate pkgconfig，这样会与arch源的pkgconfig冲突。

​	一个解决方案是将自己编译的opencv生成opencv4.pc改名，避免冲突，改名同时需要将/etc/ld.so.conf.d/opencv4.conf改为同名.