---
layout: post
title: Goto语句
---
很多程序员可能听说过，“不要使用Goto语句”这样的观点。但是现在计算机系的学生可能很少有听到过这个原则背后有着怎么样的历史背景了。关于是否使用Goto语句、什么场合使用Goto语句曾经在编程史上引发激烈的辩论，这些争论从上个世纪的60年代一直延续到本世纪初，其中的参与者包括大名鼎鼎Dijkstra Edsger、Knuth Donald还有Linus Troald等等，大部分同学应该对这些计算机发展史上里程牌式的名字非常熟悉。以现在的眼光回去看当时这些大牛们的文章，可能会觉得这些争论已经了无意义或者类似的问题已经不复存在了，但是设身处地的回到他们各自的时代，就会看到他们的讨论对计算机编程语言演进的影响。

上个世纪60年代，计算机发展还处于上古时代。硬件方面还是使用晶体管作为逻辑运算单元，软件方面支持多任务的现代操作系统也还未成型，当时的计算机主要还是用来做科学运算的工具。在这样的历史背景下，1968年3月，Dijkstra在ACM发表了一篇文章Go To Statement Considered Harmful，阐述了他对Goto语句以及编程方便的一些观点。在这篇文章中，Dijkstra认为”Goto使用的越多，程序的质量越差“，所以程序中要尽可能的消除Goto语句。这样做目的很简单，就是要尽可能的消除程序中的面条式代码（Spahetti Code）。

![_config.yml]({{ site.baseurl }}/images/spaghetti-code-wood.jpg)

什么是面条式代码呢？它指的是计算机程序中那些逻辑复杂、各分支之间跳来跳去绞在一起的代码。说到这，就不能不先了解一下当时流行的编程语言了。Dijkstra发表文章时，计算机语言还没有Swtich，For, While等控制结构，当时的程序员只能使用if-thens+goto来实现我们现在经常使用各种控制功能。但是，计算机的本质上是一个有限状态机，通过状态之间条件跳转或者循环实现各种功能。因此，在底层机器码和汇编代码级别是不可能消除跳转指令的。重复代码的使用或者说面条式的代码是伴随着计算机诞生而无法消除的，Dijkstra在他的文章中也承认了这一点。例如，由汇编语言编写的代码就是一种面条式的代码，而我们不可能消除汇编代码的jmp跳转指令。

Dijkstra发表这篇文章之后，引发了业界关于Goto语句大讨论，很多人对Dijkstra的观点感到不安，Dijkstra也因此收到了很多别人辱骂他的信件。与此同时，结构化编程（Structure Programming）的理论也正悄悄的发展起来，结构化编程试图在计算机语言中引入子函数、循环控制、条件控制等概念，包括for, while, do while, switch case等等，能够让写出来的程序更加容易修改和维护。1972年，Knuth发表了一篇文章Structured Programming with Go to statements，总结了Goto语句的历史背景、目前业界使用情况以及他自己的一些观点：Knuth 认为大部分情况下最好避免使用goto语句，但是在一些情况下，在程序中使用Goto语句是无害的，比如错误处理 、嵌套循环；还有比如even indicator。有意思的是，Knuth提到的有些情况现在已经发展成发展成现代编程语言的特性了，比如even indicator就是后来我们在各种编程语言中常常见到的switch case。

时间转到2003年，有个Linux内核开发新人从Linux的内核代码里找到了一段使用Goto的代码，他把这段代码修改了一下——消除了Goto语句，但保持代码的功能不变，然后通过邮件发到LKML(Linux Kernel Mail List)里问Linus他的这种方式是不是更加合理。Linus大神刚开始还是心平气和的和跟这位新手说：合理的使用Goto可以让代码更加清晰；此外，对于使用一些不支持描述性跳转标签的语言编程时，则尽量不要使用Goto语句。接下来这位新手又回复说，他以前经常被教导"任何使用，都要使用Goto语句“之类的云云。然后Linus就开喷了，说他这样的程序员是被计算机系的教授洗脑了，实际上if-else也是一种Goto语句，for、do-while也都是变相的Goto语句。

这封在Linux内核邮件讨论组的邮件最后，有一个内核的开发人员说了句话可以作为这篇博客结尾：软件开发人员不要把精力放在语言使用的宗教问题上，而应该把注意力放到Bug Fixing, New Feature Development等产品本身的问题上。
