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
```