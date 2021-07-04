---
title: "1.3 搭建开发环境"
weight: 3
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

我们在前一节快速体验了eggos的基本功能，这一节我们搭建一下eggos的开发环境，为后面的深入分析eggos打下基础。

# 环境准备

基本工具如下：

- go1.13.x
- mage构建工具
- gcc和gdb，需要能生成elf32格式的。
- qemu虚拟机

各平台的安装步骤如下所述。

## Linux(ubuntu)

### Go语言编译器

``` sh
$ curl -LO https://golang.google.cn/dl/go1.13.15.linux-amd64.tar.gz
$ mkdir $HOME/local && tar xf -C $HOME/local go1.13.15.linux-amd64.tar.gz
# 将$HOME/local/go/bin加入PATH环境变量，这里以bash为例
$ echo 'export PATH=$HOME/local/go/bin:$PATH' >> $HOME/.bashrc
# 将$HOME/go/bin也加入环境变量，方便执行编译出来的go工具
$ echo 'export PATH=$HOME/go/bin:$PATH' >> $HOME/.bashrc
$ source $HOME/.bashrc
# 设置GOPROXY环境变量，加速package下载
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn
```

按照上面命令执行完毕之后，输入`go version`命令，如果能正常输入go版本号就证明安装成功了。

### mage构建工具

mage是一个类似make的构建工具，但它的makefile是用go语言编写的，可以跨平台，eggos最初是用的makefile，后面切换到mage上了。安装mage也非常简单。

``` sh
$ go get github.com/magefile/mage
```

### C语言开发套件

``` sh
$ sudo apt-get install build-essential gdb
```

### Qemu虚拟机

``` sh
$ sudo apt-get install qemu-system-x86
```

## MacOS

### Go语言编译器

``` sh
$ curl -LO https://golang.google.cn/dl/go1.13.15.darwin-amd64.tar.gz
$ mkdir $HOME/local && tar xf -C $HOME/local go1.13.15.darwin-amd64.tar.gz
# 将$HOME/local/go/bin加入PATH环境变量，这里以zsh为例
$ echo 'export PATH=$HOME/local/go/bin:$PATH' >> $HOME/.zshrc
# 将$HOME/go/bin也加入环境变量，方便执行编译出来的go工具
$ echo 'export PATH=$HOME/go/bin:$PATH' >> $HOME/.zshrc
$ source $HOME/.zshrc
# 设置GOPROXY环境变量，加速package下载
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn
```

按照上面命令执行完毕之后，输入`go version`命令，如果能正常输入go版本号就证明安装成功了。

### mage构建工具

``` sh
$ go get github.com/magefile/mage
```

### C语言开发套件

``` sh
$ brew install x86_64-elf-binutils x86_64-elf-gcc x86_64-elf-gdb
```

### Qemu虚拟机

``` sh
$ brew install qemu
```