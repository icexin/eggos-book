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

我们在上一节快速体验了eggos的基本功能，这一节我们搭建一下eggos的开发环境，为后面的深入分析eggos打下基础。

# 核心工具准备

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

# IDE开发环境

我们使用`vscode`来开发eggos。项目默认附带了vscode的配置文件，直接使用vscode打开eggos项目即可开始修改代码。

## 安装的插件

- [Go语言插件](https://marketplace.visualstudio.com/items?itemName=golang.go)，用于代码补全，跳转与高亮
- [GDB插件](https://marketplace.visualstudio.com/items?itemName=webfreak.debug)，用于对内核进行debug，如断点调试

## Debug配置

需要根据实际情况稍微修改一下`.vscode/launch.json`里面的配置

``` json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
         {
            "name": "dlv-gdb",
            "type": "go",
            "request": "attach",
            "mode": "local",
            "cwd": "${workspaceFolder}",
            "debugAdapter": "legacy",
            "processId": 1234,
            "backend": "gdbstub",
            "dlvFlags": [
                "${workspaceRoot}/kernel.elf",
            ],
        },
        {
            "type": "gdb",
            "request": "attach",
            "name": "Attach to qemu",
            "executable": "./kernel.elf",
            "target": ":1234",
            "remote": true,
            "cwd": "${workspaceRoot}",
            "valuesFormatting": "parseText",
            // 这里根据情况进行替换，linux就用gdb
            "gdbpath": "gdb", 
            // MacOS用下面配置
            // "gdbpath":"x86_64-elf-gdb",  
        },
    ]
}
```

具体怎么用debugger来调试eggos，在后面的章节会写，先按这样配置环境。

另外，原生的`dlv`并不支持qemu的gdb stub，笔者修改了一个版本，如果想用dlv来调试eggos的话，可以按照下面步骤安装dlv

``` sh
$ git clone --branch gdb https://github.com/icexin/delve.git
$ cd delve
$ go install ./cmd/dlv
```

# 编译运行eggos项目

## 下载代码

``` sh
$ git clone https://github.com/icexin/eggos.git
```

## 编译项目

``` sh
$ mage -v multiboot
```

如果没有报任何错误的话，表示编译很顺利，目录下应该有一个`mutiboot.elf`文件，这个即是我们的内核文件。

## 运行eggos

``` sh
# 如果有GUI的话，可以加上这个环境变量，让qemu运行于独立窗口
$ export QEMU_GRAPHIC=true
$ mage qemu
```

{{<hint info>}} 如果在终端下运行qemu，可以通过<Ctrl+A C>唤出qemu的console，再输入`quit`退出qemu模拟 {{</hint>}}

一切顺利的话，就能看到eggos的启动界面了，至此我们完成了eggos的编译和运行。

# 小结

这一节我们完成了开发环境的搭建，同时完成了eggos项目的编译，并使用qemu虚拟机启动了编译出来的内核。这一节的工作为后面我们编写eggos程序打下了基础。

下面我们编写第一个eggos程序。