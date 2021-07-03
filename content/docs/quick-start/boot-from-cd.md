---
title: "1.1 从ISO镜像启动"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

本节将帮助大家下载ISO镜像，并从虚拟机启动eggos。

# 下载镜像文件

直接使用ISO镜像来启动eggos，可以免去我们编译整个项目的麻烦，特别是我们刚开始的时候想快速体验eggos的功能的时候。

eggos在每个版本会生成一个ISO镜像文件，我们可以从github的release界面直接下载，从网址 https://github.com/icexin/eggos/releases 进入到eggos的release界面，点击`eggos.iso`下载。

# 虚拟机启动ISO文件

我们以linux系统为例，windows系统可以用wsl的方式来启动linux子系统，操作类似。

ubuntu的用户可以通过命令 `sudo apt install qemu-system-x86` 来安装qemu虚拟机。

使用如下命令来启动虚拟机:

``` sh
$ qemu-system-x86_64 -m 256M -no-reboot -serial mon:stdio -netdev user,id=eth0,hostfwd=tcp::8080-:80 -device e1000,netdev=eth0 -cdrom eggos.iso
```

{{<hint info>}} 没有图形界面终端，如windows的`wsl`需要加上`-nographic`，从而以非图形化方式启动qemu {{</hint>}}

顺利启动后的画面如下：

![启动后的画面](/images/eggos-startup.png)