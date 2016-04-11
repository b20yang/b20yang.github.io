---
layout: post
title: Clean Code Contest
---

5月份公司举办了Clean Code Contest，比赛形式是：主办方给出一份内部工具的源代码，要求在这个基础版本上加入一个非常微小的功能（这个值得一提，为了避开为重构而重构的原则），参赛者通过自己对Clean Code的理解修改这份代码、做好单元测试，然后在截止期前把源代码提交到代码库。时间一个月左右，5月底最后一天冻结代码。比赛的前三名可以拿到一个Nokia的Lumia 800。

![_config.yml]({{ site.baseurl }}/images/clean_code_contest.jpg)

我厚着脸皮报名参加了。一是Lumia 800确实是一部非常漂亮的手机，二是在觉得自己这方面做的还很欠缺，如果能够通过参加这个比赛驱动自己看一些书——Learn By Doing，即使最后没能够拿到奖品，也至少会有一些的收获。最后很欣慰，我确实把整个5月份的大部分业余时间用来看Code Complete, Clean Code Handbook, Refactoring这几本书了。然后写代码大概花了一个周末加上7、8个工作日晚上的时间，到最后时间有点仓促，提交的代码也很粗糙。6月份的开发者大会上能拿到Lumia 800，自己都觉得非常意外。

维基百科上的相关词条是这样定义Clean Code的： 
>software code that is formatted correctly and in an organized manner so that another coder can easily read or modify it.   

这段定义主要是强调了代码格式和代码的结构这两个方面，相比与此，我更喜欢Clean Code Handbook 里面引用C++作者Bjarne Stroustrup对Clean Code的理解：  

>I like my code to be elegant and efficient. The logic should be straitforward to make it hard for bugs to hide, the dependencies minimal to ease maintenance, error handling complete according to an articulated strategy, and performance close to optimal so as not to tempt people to make the code messy with unprincipled optimizations. Clean code does one thing well.

Clean Code应该具有优雅的格式，清晰的逻辑结构以及良好的效率，Bjarne重点强调了程序的执行效率。其实具体每一点展开都有很多的内容，比如优雅的格式小包括代码块的格式、注释的形式、变量函数结构等符号的命名；逻辑结构包括如何更好抽象数据结构、更好的定义函数（包括参数列表啊函数功能等）、以及更好的模块结构以及统一的错误处理。

而在这次比赛里，自己做到的仅仅只是Clean Code的皮毛。在模块化方面，我把对操作系统的依赖、对外部工具的依赖以及对字符串处理等部分分离出来形成独立的模块，然后提供统一的外部接口。这样做的理由有非常多，比如软件工程里面的原则Don't Repeat Yourself、比如更加易于单元测试、比如让应用逻辑更加清晰等等。模块化的同时也基本上保证了可扩展性，像模块化后直接可以使用外部工具的API替换目前的实现方式。这是做的稍好的方面。

而在应用层代码的抽象数据结构设计和子程序的设计上，则完成的有点粗糙——这也是在代码提交之后让我垂头丧气的原因。比如在抽象数据结构的设计上，直接把不同逻辑层次的数据一箩筐的往一个数据结构里面放，命名为s_context，然后靠着这个抽象数据结构行走江湖，这种设计方法实在太暴力了。在子程序的设计上，虽然我反复的阅读了Code Complete的高质量子程序设计的那一章，也努力尝试做到低耦合和高内聚，但是依然做的不够好。

这算是一个流水账式的总结，Clean Code路漫漫。乔布斯说：如果你是个木匠，你要做一个漂亮的衣柜，你不会用胶合板做背板，虽然这一块是靠着墙的，没有人会看见。你自己知道它就在那里，所以你会用一块漂亮的木头做背板。如果你想晚上睡得安稳的话，就要保证外观和质量都足够好。

