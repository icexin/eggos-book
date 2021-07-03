---
title: "1.2 体验eggos"
weight: 2
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---
# 体验eggos

上一节我们通过ISO镜像启动了eggos系统。

启动`eggos`成功后，我们就可以体验一些内置功能了。默认情况下有个类似linux的终端界面，在这个终端界面下我们可以输入命令来操作`eggos`。

这一节我们来体验下`eggos`的一些功能。

## Javascript解释器

输入如下命令来启动交互式Javascript解释器

``` sh
$ js
```

之后我们就可以输入合法的Javascript语句来执行。

``` Js
>>> console.log("hello world")
hello world
>>> reg = new RegExp("hello")
>>> console.log(reg)
/hello/
>>> resp = reg.test("hello world")
>>> console.log(resp)
true
```

这个Javascript解释器内置了一个简单的`http.Get`方法，用于从一个url里面获取内容。

``` Js
>>> var url = "http://baidu.com"
>>> resp = http.Get(url)
>>> console.log(resp)
<html>
<meta http-equiv="refresh" content="0;url=http://www.baidu.com/">
</html>
```

它甚至有自动补全功能，输入`http.`之后敲击`<TAB>`键，就可以补全出`http.Get`。重复按`<TAB>`可以在多个候选结果间切换。

按`<Crtl+D>`来退出Javascript解释器。

## 文件系统

`eggos`现在没有实现完整的文件系统，而是使用了[afero](https://github.com/spf13/afero)作为文件系统的抽象接口。

我们通过`mount`命令挂载一个`samba`文件系统来体验`eggos`的文件系统功能。

``` sh
root@eggos# mount smb://icexin:eggos@172.28.90.3:445/sambashare /share
root@eggos# cd /share
root@eggos# ls
-rw-rw-rw- 111 fib.js
root@eggos# cat fib.js
function fib(n) {
        if (n == 1 || n == 2) {
                return 1;
        }
        return fib(n-1) + fib(n-2);
}

console.log(fib(10))

root@eggos# js fib.js
55

```

解释一下上面我们做了什么：

首先我们通过mount命令将一个samba地址挂载到了本地的一个目录

- `mount`的第一个参数是源地址URI，第二个参数是本地的挂载点，这里是`/share`
- 源地址URI符合标准的URI格式，其中`smb`代表我们使用的是`samba`协议
- `icexin`和`eggos`分别是samba的账号和密码，读者需要替换成自己的账号和密码
- `172.28.90.3:445`为samba服务器的ip和端口，读者需要替换成自己的ip和端口

接着我们使用`cd`和`ls`命令切换到`/share`目录，并列出目录下的文件，目录下有一个`fib.js`。

然后我们使用`cat`命令打印出`fib.js`文件的内容，里面使用Javascript实现了斐波那契数列算法，并打印出第10个斐波那契数。

最后我们执行`js`命令，用`fib.js`作为参数，效果是执行了`fib.js`里面的Javascript代码，打印出结果`55`。

## HTTP服务

`eggos`内置了一个简单的http服务器，使用`go httpd`命令即可后台启动HTTP服务器。
这个服务器默认绑定了两个地址：

- `/debug/pprof`，go著名的pprof地址，里面可以查看很多当前go进程的debug信息
- `/fs/`，根目录的映射，可以从浏览器访问整个文件系统。

打开浏览器，输入`http://127.0.0.1:8080/debug/pprof/`，即可打开debug页面，从里面我们可以获取当前运行的goroutine堆栈快照。

![http pprof信息](/images/http-pprof.png)


输入`http://127.0.0.1:8080/fs/`即可访问文件系统根目录，从里面我们可以发现刚刚挂载的`/share`目录。

![fib.js的内容](/images/fib-js.png)

一路点进去之后我们就能访问`/share/fib.js`的内容。

{{<hint info>}} 很遗憾的是目前`httpd`一旦启动就没办法停止，只能重新启动eggos来停止服务 {{</hint>}}

## NES模拟器

{{<hint warning>}} 本小节必须在图形界面下运行qemu {{</hint>}}

`eggos`内置了一个nes模拟器，也就是我们小时候玩的小霸王游戏机模拟器，可以运行一些简单的任天堂FC游戏。

接着文件系统那一节的内容，我们这次需要在共享文件夹里面准备一个nes rom。

``` sh
root@eggos#  mount smb://icexin:eggos@172.28.90.3:445/sambashare /share
root@eggos# ls /share
-rw-rw-rw- 111   fib.js
-rw-rw-rw- 40976 mario.nes
```

相比于之前，多了一个`mario.nes`文件，这个就是我们即将运行的`超级马里奥兄弟`游戏。

执行如下命令即可启动nes模拟器，其中`-rom`参数指定待运行的rom文件。

``` sh
root@eggos# nes -rom /share/mario.nes
```

如果一切成功的话，可以看到如下画面

![nes-mario](/images/nes-mario.png)

操作按键是固定的，分别如下:
- `W`, `S`, `A`, `D`控制手柄的上下左右
- `K`, `J`控制手柄的`A`和`B`
- 空格和回车控制手柄的选择和开始
- `Q`键可以退出游戏

{{<hint info>}} 如果不开启模拟器加速的话，运行会比较卡，MacOS用户可以通过在启动qemu的时候添加`-M accel=hvf`开启硬件加速，linux用户可以添加`-M accel=kvm`来启动加速。{{</hint>}}


# 小结

通过Javascript解释器，文件系统，HTTP服务，NES模拟器这几个应用我们对eggos有了初步的认识，下面章节我们开始搭建eggos开发环境，编写自己的eggos应用。

# 扩展阅读

- [1] A JavaScript interpreter in Go https://github.com/robertkrimen/otto
- [2] Ubuntu上安装samba服务 https://ubuntu.com/tutorials/install-and-configure-samba
- [3] NES emulator written in Go  https://github.com/fogleman/nes
