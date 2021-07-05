---
title: "1.4 hello eggos"
weight: 4
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: false
# bookSearchExclude: false
---

这一节我们编写第一个eggos的应用程序。

eggos是一个[unikernel](https://en.wikipedia.org/wiki/Unikernel)，但不妨碍在里面加上功能子模块。

一些简单的命令放在`app/cmd`目录下，稍微复杂一点的命令需要单独放一个目录。

# 第一个子程序

打开编辑器，在`app/cmd`目录下新建`hello.go`，写入如下代码：

``` go
package cmd

import (
	"fmt"

	"github.com/icexin/eggos/app"
)

func helloMain(ctx *app.Context) error {
	fmt.Fprintln(ctx.Stdout, "hello eggos")
	return nil
}

func init() {
	app.Register("hello", helloMain)
}
```

运行 `mage -v qemu`，等待终端出来之后，键入 `hello`，如果能顺利输出如下内容，则证明我们的程序已经被顺利编译并运行。

``` sh
welcome to eggos
root@eggos# hello
hello eggos
```

# 代码解释

每个子程序都有一个入口，函数签名为`func (ctx *app.Context) error`，最终在`init`函数里面调用`app.Register`即可注册子程序，
之后就在命令行里面调用这个子程序了。

`app/cmd`目录下还有很多其他子程序可以参考。