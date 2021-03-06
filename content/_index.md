---
title: 简介
type: docs
---

# 关于这个项目

上大学的时候，在实验室偶然发现了一本《自己动手写操作系统》，当时我看到这本书的时候非常惊讶，因为在我的认知里面，操作系统是一个非常复杂的东西，是类似Linus那样的大神才可以掌握的魔法。随着我一点一点读完这本书，也开始了解了什么是boot loader，分段与分页管理在代码上是怎么表现出来的，内核空间与用户空间在cpu视角的差别，最惊奇的还是进程切换的魔法。代码一个字节一个字节在硬件上跑的感觉跟看纯操作系统理论是完全不一样的体验，由于我是非科班出身，当时的那个小操作系统内核项目基本就代替了我的操作系统课程，现在偶尔看看那时的代码还是有很多感慨的。

调试内核不比普通的应用程序，难度要大很多，没有人指引的阶段，我踩了很多坑。那个内核项目花了我小半年的时间。到最后还是有很多bug没有解决。然而即使困难重重，还是实现了基本的内存管理，多进程，elf loader等功能。

工作后接触了Go语言，使用这个语言越多，越发现这门语言跟c语言很像：平坦的内存，指针，翻译机器指令等等。爱折腾的我就萌生了一个想法，能不能用Go重写一遍操作系统内核？

另外，Go的runtime提供了一些基本的操作系统抽象，goroutine对应进程，channel对应进程间通信，另外Go有自己的虚拟内存管理，所以如果用Go来编写内核，进程调度和内存管理这些繁琐的东西就直接可以用现成的了，想想就很有趣。

然而当时这个想法也只是停留在脑袋里面发酵，没有产生实际的行动，我还在google group上提了一个问题，考古的同学可以看这里 [https://groups.google.com/g/golang-china/c/I1sAcjpihoY](https://groups.google.com/g/golang-china/c/I1sAcjpihoY)

在疫情期间，媳妇回娘家休产假了，一个人在北京的我又重新思考了这个项目，事实证明，媳妇回娘家是编写操作系统的第一生产力，很快我就写出了第一个原型，后面又修修改改就成了现在的样子。最终取名eggos，为啥叫这个名字呢？因为我家小闺女叫蛋蛋 :) 。相比于大学时期的作品，这次的完成度比较高，移植了很多组件，包括完整的TCP/IP协议栈，NES模拟器，Javascript解释器等等。

然而挑战还是比较有的，Go在很多指令里面打桩来进行协程调度以及内存GC，在一些不能重入的地方，如中断处理和系统调用，都带来了一些麻烦。
总的来说，使用Go来操作硬件是一种乐趣，一方面也让我深入了解了Go的runtime，另一方面也是提供了一种除了c语言之外在裸机上编写操作系统内核的尝试。

现在想把我在这个项目里面的探索书写出来，让更多有志于动手写操作系统以及了解go runtime的同学有一个可以参考的地方。

为探索者点亮一盏明灯，使其在求知的路上不再迷茫和徘徊。
