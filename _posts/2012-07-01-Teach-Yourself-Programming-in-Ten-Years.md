---
layout: post
title: 十年学会程序设计
---

_翻译：杨彪 原作者：Peter Norvig 原文链接：<a href="http://norvig.com/21-days.html">Teach Yourself Programming in Ten Years</a>_

**为什么每个人都如此的匆忙？**

走进任何一家书店，你都会看到类似七天学会JAVA程序设计、几小时或者几天学会Visual Basic, Windows, Internet等书籍。我在亚马逊网上书城尝试了如下搜索：

>出版日期：1992年之后 和 标题包含：天 和 （标题包含：学习 或 标题包含：自学）

最后得到248个返回结果，其中排在前面的78本书都是计算机书籍（第79本是《三十天学会孟加拉语》）。如果我把搜索条件中的“天”替换成“小时”，也能得到惊人相似的返回结果：总共超过253本书符合条件，其中前面77本都是计算机书籍，第78本是《二十四小时自学语法和风格》。在前200个结果中，有96%是计算机书籍。

从上可得出的推论就是：要么人们都想急切的学会计算机编程，要么学习计算机编程比学习其他东西更加容易。我们的书店里面并没有几天学会贝多芬、几天学会量子物理等书籍，甚至连几天学会宠物美容的书籍都没有。在《程序设计方法》这本书中，傅雷森直言不讳的说“编写糟糕的程序并不是什么难事，即使笨蛋也可以在21天内学会“，也是对人们这种浮躁心理的认同。

如果一本书叫三天学会C++，我们来分析一下这意味着什么：

* 学会：在三天中，你都没时间写几个有意义程序，从不断正确或者错误的尝试中学习程序设计；你也没有时间和有经验的程序员交流，看看他们是如何使用C++编程的。总之，在三天里你根本学不到太多东西。这样的书只能让你对C++有个肤浅的认知，而不是深入理解这门语言。就像诗人亚历山大-蒲柏所说的那样，一知半解是一件危险的事情。
* C++：在三天中，你可能可以学会C++的部分语法（如果你已经学过其他编程语言），但是你不可能学到太多关于如何正确使用这门语言的知识。简而言之，如果你是个初级程序员话，你可能可以学会使用C++的基本语法编写程序，但是你不可能体会到C++真正的优点（或者缺点）。这有什么意义呢？艾伦·佩利曾说过：”如果一门编程语言对你的编程思维方式没有造成影响，那么这门编程语言根本不值得去学习“。一种可能的原因是，为了完成一项具体任务，你需要学习点C++的知识才能完成和现有工具接口交互。那么其实你根本不是在学习程序设计，你只是在试图完成一项任务而已。
* 三天：不幸的是，就像下节谈到的那样，三天远远不够。


**十年学会程序设计**

有研究者表明在任何领域大部分人需要大概十年时间的持续学习才能达到专家级水准，包括下棋、音乐创作、电报操作、绘画、弹钢琴、游泳、网球，还有神经心理学研究和拓扑学研究等领域。其中的要点就是刻意的练习：不仅仅是反反复复的练习，而且需要不断挑战自我，尝试更难的任务、分析完成的情况、修正出现的错误。然后不断地重复这个过程。这看起来没有任何捷径：即使是莫扎特——四岁时就开始展现出惊人的音乐天赋，也是通过13多年的练习之后才开始创作世界级的音乐。另外一个例子，在1964年，披头士乐队好像突然就走进大众的视野火了起来，发行了非常叫卖的唱片并且出现在埃德·沙利文的电视节目中。你要知道的是，自从1957年以来他们就开始在利物浦和汉堡的小酒吧里面驻唱；而在取得大规模的影响力之后，直到1967年，他们发行了Sgt. Pepper专辑后才取得真正意义上的成功。马尔科姆·格拉德威尔曾经对柏林音乐学院的学生做过研究，他统计了学生们过去在音乐学习上花的时间，然后分别对表现优异、中等和一般的三类学生做了比较：
>在这三组中，每个人几乎都在相同的年龄开始练习演奏——大约5岁左右。在刚开始的前几年中，每个人的练习总时间都差不多——每周两到三个小时左右。但是在八岁左右时，他们之间开始出现了差异。那些优秀的学生开始比其他学生练习更长的时间：九岁时每周六小时，12岁时每周八小时，14岁时每周16小时，越来越多，直到20岁时他们每周练习时间已经超过30个小时。到20岁时，那些一流演奏者的练习时间总计超过10000个小时。相比之下，表现良好学生总计用了8000个小时，而那些未来的音乐教师仅仅只是超过4000个小时。

看起来10000个小时可能就是那个神奇的时间点，而不是前面说的10年。（摄影师亨利·卡蒂尔·布雷松曾说过”你的前10000张照片都是废片“，不过他每个小时按快门次数肯定不止一次）。塞缪尔·约翰逊（1709-1784）则认为需要更长的时间：”任何领域都需要一生的不懈努力才能做到出类拔萃，而不是随随便便就能成功“。还有作家杰弗里·乔叟（1340-1400）也曾说过”生命如此短暂，而学习技能又如此的缓慢“。古希腊医学之父希波克拉底（约公元前400年）有一句非常著名的语录——”ars longa, vita brevis“，它是这个长句的一部分——”Ars longa, vita brevis, occasio praeceps, experimentum periculosum, iudicium difficile”， 中文翻译过来就是这样的：“人生苦短，学艺艰辛，时光飞逝，世多歧路，难以抉择”。尽管在拉丁语中，ars也可以指艺术（art）或者技能（craft），但是在古希腊语中thechne这个词指的是技能（skill），而不是艺术（art）。

**如果你想成为一个程序员**

下面是我对学好程序设计的一些体会：

* 对编程感兴趣，你写代码只是因为它很好玩。并且你要确保它一直足够的好玩，这样才可以让你心甘情愿地花费十年或者10000个小时持续学习。
* 动手编程。最好的学习方法就是边做边学。从更加专业的角度来讲，个体在特定领域的最高成就不是由经验的延伸自动获得的，而是经验丰富的个体通过刻意努力不断自我突破的结果。而最有效的学习方法需要一个针对每个人量身定做的难度适当的任务，得到及时的反馈，然后反复练习并纠正错误。这些观点也可以从书籍在实践中学习认知：心智，数学和日常生活的文化得到参考。
* 和其他程序员交流，阅读别人写的代码。这比你在阅读任何书籍和参与培训课程学到的更加重要。
* 如果你愿意的话，花四年时间上大学（或者读研究生）。这样让你有机会得到那些需要学位证书的工作，并且深入了解那些领域。但是如果你不喜欢呆在学校，你也能通过自学或者在工作得到学到相同的知识。但无论如何，仅仅通过书本的学习是远远不够的。新黑客词典的作者，Eric Raymon，说过“计算机科学的教育并不能够使任何人称为一个编程专家，就像研究画刷和颜料不可能让人成为画家一样”。虽然Eric只有高中文凭，但他是我招聘过的最好的程序员之一。他编写过很多非常优秀的软件，有自己的新闻组，并且还有持有一家夜店的大量股份。
* 和其他程序员一起做项目。在一些项目中做最好的程序员，而在其他项目中做最差的那个程序员。当你做最好那个时，你可以锻炼你领导一个项目的能力以及你的观点对他人的影响力。当你做最差时，你可以学习领导项目的人做什么和他们不做什么（因为他们想让你按他们的想法做事）。
* 维护其他程序员做的项目。理解他们写的程序。在原作者不在的时候，看看你是如何理解并修正他们的代码的。然后想想，假如你来设计这个程序的话，你要怎么做才可以让之后维护你代码的人更加轻松。
* 至少学习六门编程语言。包括一门支持类抽象的语言（比如Java或者C++），一门支持函数抽象的语言（比如Lisp或者ML），一门支持语法抽象的语言（比如Lisp），一门描述性的语言（比如Prolog或者C++模板），一门支持并程技术的语言（比如Icon或Scheme），还有一门支持并行的语言（比如Sisal）。
* 记住在“计算机科学”里包含了“计算机”这个词。所以你应该知道你的计算机执行一条指令需要多长时间，从内存中读一个字需要多长时间（cache命中或不命中），从磁盘中读取连续数据需要多长时间，以及在磁盘中访问一个新地址需要多长时间。（请参考答案)
* 参与语言一门编程语言的标准化工作。它可以是ANSI C++委员会，或者也可以具体到决定你们的编程风格中是2个空白符还是4个空白符对齐这样的工作。不管哪种方式，你都可以学习到其他人喜欢一门语言的哪些东西，他们对这门语言的体会有多深，以及还有可能了解到他们为什么会有这些体会。
* 尽可能快地脱离语言标准化工作。

了解到这些之后，你觉得仅仅通过书本的学习能在编程之路上走多远？在我第一个孩子出生之前，我阅读了所有How to的编程技术书籍，但感觉还像个什么都不会的新手。30个月之后——当我第二个孩子来到了这个世上时，我并没有回去重新复习这些书。取而代之的是，我凭着个人经验不断去实践——最后证明远比那些书籍更有用，才打消了之前对自己编程上的很多疑问。

在《没有银弹》这本书中，佛瑞德·布鲁克斯提出了如何培养优秀软件设计师的三步计划

1. 尽可能早地、全方位地找出那些非常有潜力的设计师。
2. 给这些设计师安排导师，指导他们的职业生涯，并且记录他们的进展。
3. 给这些成长中的设计师提供交流和促进的机会。

以上假设这些人已经具有了成为优秀软件设计师必要品质，接下来你只要引导他们走上正轨。艾伦·佩利则说的更加直白：“你可以教任何人如何雕塑，但米开朗基罗就用不着教。对伟大的程序员也是如此”。佩利的意思是说优秀的人具有一些内在品质，而这些品质是培训或教育所不能给予的。那么这些品质从何而来？与生俱来的吗？还是通过后天的勤奋发展起来的？Auguste Gusteau(电影料理鼠王的主角)把它归结为对未知的无所畏惧，“每个人都能烹饪，只有那些无畏的人才能成为大厨师”。而我认为这更多是把一生的大部分时间奉献给勤奋实践的这种意愿。但这可能也可以总结成对未知的无畏。或者，就像Gusteau的批评者，Anton Ego说的：“并不是每个人都可以成为伟大的艺术家，但是一个伟大的艺术家可能来自任何地方”。

所以，不要犹豫去买Java/Ruby/Javascript/PHP的书籍，你有可能会从中学到很多东西。但如果你不想改变你的生活，或者不想为了多学点程序设计的专业知识，而花上24小时、数天、或者数周。那么如何做到24个月持之以恒、不懈努力去改进呢？说到这，我想你应该得到了它...

**参考资料**

Bloom, Benjamin (ed.) Developing Talent in Young People , Ballantine, 1985.  
Brooks, Fred, No Silver Bullets , IEEE Computer, vol. 20, no. 4, 1987, p. 10-19.  
Bryan, W.L. &amp; Harter, N. "Studies on the telegraphic language: The acquisition of a hierarchy of habits. Psychology Review, 1899, 8, 345-375  
Hayes, John R., Complete Problem Solver Lawrence Erlbaum, 1989.  
Chase, William G. &amp; Simon, Herbert A. "Perception in Chess" Cognitive Psychology, 1973, 4, 55-81.  
Lave, Jean, Cognition in Practice: Mind, Mathematics, and Culture in Everyday Life , Cambridge University Press, 1988.  

**参考答案**

一台典型的PC上各种操作的大概时间：
执行一条指令（大部分） 1/1,000,000,000秒 = 1纳秒
从L1缓存去数据或指令 0.5纳秒
分支预测失败 5纳秒
从L2缓存去数据或指令 7纳秒
互斥锁/解锁 25纳秒
从内存从取数据或指令 100纳秒
通过1Gbps网络发送2KB数据 20,000纳秒
从内存读取1MB的连续数据块 250,000纳秒
从磁盘的新位置获取数据 8,000,000纳秒
从磁盘读取1MB的连续数据块 20,000,000纳秒
从美国发送一个数据包到欧洲并返回 150毫秒 = 150,000,000纳秒

**附录：语言选择**

好几个人曾问过我他们应该从哪门语言入手开始学习编程。这个问题没有统一的答案，不过你可以参考以下这几点：

* 看看你周围的人。当有人问“我应该使用哪个操作系统，Windows，Unix，还是Mac”时，我的答案通常是：“跟你周围的人用一样的就行”。你从你朋友那里学到的知识可以弥补这些操作系统或者编程语言内在的差异。也考虑一下你有可能接触的人：那些你将来有可能成为其中一员的程序员社区。你选择的语言的社区是否在快速成长，还是他们都已经岌岌可危了？是否有足够的书籍、网页、在线论坛能够解答你的疑问？你喜欢这些社区里面的人吗？
* 尽可能简单。像C++和Java这样的语言是为了大规模团队的专业开发设计的，使用它们的程序员通常很有经验并且专心他们代码的实时运行性能。所以，这些语言引入了很多复杂的部分支持这些场景。但你只是学习程序设计，你不需要那么复杂。你仅仅需要一个易于学习的编程语言。
* 练习。你更喜欢用那种方法学习钢琴：交互式方法，你每按一个钢琴键就可马上看看这个什么音符；还是“批量”模式，听完一首歌曲之后才去看看所有音符呢？显而易见，交互模式可以让学习钢琴更加容易，这对学习编程也一样。所以坚持用交互式的方法去学习和使用一门语言

有了这些准则后，我推荐初学者使用Python或Scheme作为学习编程的第一门语言。但是每个人的背景可能大不相同，所以也应该有其他很多的选择。如果你只有十几岁的话，你可能更加喜欢Alici或者Squeak（大点的初学者也可能会喜欢）。最重要的是，选择一门语言，然后努力开始学习。

**附录：书籍和一些资源**

一些人问我他们应该从哪些书籍或者网页开始学习。虽然反复强调“书本的学习远远不够”，但我还是可以推荐下面这些资料：

* Scheme: Structure and Interpretation of Computer Programs (Abelson & Sussman) 这本书可能是最好的计算机科学入门书籍，它教导人们如何使用计算机思维方式来编程。你可以看看这本书的在线视频课程，还有电子文稿。这本书很有挑战性，可能会吓走那些本来可以成为优秀程序员的人。
* Scheme:How to Design Programs (Felleisen et al.), 这本书是关于如何设计出优雅和实用程序的最好书之一。
* Python: Python Programming: An Intro to CS (Zelle), 很好的Python入门书籍。
* Python: Python.org 上一些在线的入门资料。
* Oz: Concepts, Techniques, and Models of Computer Programming (Van Roy &amp; Haridi) 。这本书被认为是Abelson &amp; Sussman当代的继任者。讲的是关于程序设计的一些宏观的方法论，比Abelson &amp; Sussman的书籍覆盖面更广，却更加容易学习和遵守。它采用了Oz编程语言——非常小众，但是可以作为学习其他语言的基础。

（译注：基于2007年更新的版本翻译，有几处难以理解的地方参考了<a href="http://blog.youxu.info/21-days/">徐宥老版本</a>翻译）
