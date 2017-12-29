# 初识nodeJS

　　几年前，对于学习NodeJS可能还有所迟疑，怕分散了前端学习的精力。但到了现在，如果不学习nodeJS，前端的学习却可能无法再有所进展。技术的进步就是这么残酷。对新技术观望的时候，该技术已经大行其道了。本文将介绍nodeJS的基础知识

&nbsp;

### 语言选择

　　Ryan Dahl是一名资深的C/C++程序员，在创造出Node之前，他的主要工作都是围绕高性能Web服务器进行的。经历过一些尝试和失败之后，他找到了设计高性能，Web服务器的几个要点：事件驱动、非阻塞I/O，而这也正是nodejs的两大特点

　　所以Ryan Dahl最初的目标是写一个基于事件驱动、非阻塞I/O的Web服务器，以达到更高的性能，提供Apache等服务器之外的选择。写Node的时候，Ryan Dahl曾经评估过C、Lua、Haskell、 Ruby等语言作为备选实现，结论为：C的开发门槛高，可以预见不会有太多的开发者能将它用于日常的业务开发，所以舍弃它；Ryan Dahl觉得自己还不足够玩转Haskell，所以舍弃它；Lua自身已经含有很多阻塞I/O库，为其构建非阻塞I/O库也不能改变人们继续使用阻塞I/O库的习惯，所以也舍弃它；而Ruby的虚拟机由于性能不好而落选

　　相比之下，JavaScript比C的开发门槛要低，比Lua的历史包袱要少。尽管服务器端JavaScript存在已经很多年了，但是后端部分一直没有市场，可以说历史包袱为零，为其导入非阻塞I/O库没有额外阻力。另外，JavaScript在浏览器中有广泛的事件驱动方面的应用，暗合Ryan Dahl喜好基于事件驱动的需求。当时，第二次浏览器大战也渐渐分出高下，Chrome浏览器的JavaScript引擎V8摘得性能第一的桂冠。考虑到高性能、符合事件驱动、没有历史包袱这3个主要原因，JavaScript成为了Node的实现语言

&nbsp;

### 起名

　　起初，Ryan Dahl称他的项目为web.js，就是一个Web服务器，但是项目的发展超过了他最初单纯开发一个Web服务器的想法，变成了构建网络应用的一个基础框架，这样可以在它的基础上构建更多的东西，诸如服务器、客户端、命令行工具等。Node发展为一个强制不共享任何资源的单线程、单进程系统，包含十分适宜网络的库，为构建大型分布式应用程序提供基础设施，其目标也是成为一个构建快速、可伸缩的网络应用平台。它自身非常简单，通过通信协议来组织许多Node，非常容易通过扩展来达成构建大型网络应用的目的。每一个Node进程都构成这个网络应用中的一个节点，这是它名字所含意义的真谛

&nbsp;

### 特点

　　作为后端JavaScript的运行平台，Node保留了前端浏览器JavaScript中那些熟悉的接口，没有改写语言本身的任何特性，依旧基于作用域和原型链，区别在于它将前端中广泛运用的思想迁移到了服务器端。Node相较于其他语言的特点如下所示

　　1、异步I/O

　　在Node中，绝大多数的操作都以异步的方式进行调用。Ryan Dahl排除万难，在底层构建了很多异步I/O的API，从文件读取到网络请求等，均是如此。这样的意义在于，在Node中，我们可 以从语言层面很自然地进行并行I/O操作。每个调用之间无须等待之前的I/O调用结束。在编程模型上可以极大提升效率

　　以同时执行两个文件读取任务为例，异步I/O取决于最慢的那个文件读取的耗时，而同步I/O的耗时是两个任务的耗时之和。这里异步带来的优势是显而易见的

　　2、事件

　　随着Web 2.0时代的到来，JavaScript在前端担任了更多的职责，事件也得到了广泛的应用。 Node不像Rhino那样受Java的影响很大，而是将前端浏览器中应用广泛且成熟的事件引入后端， 配合异步I/O，将事件点暴露给业务逻辑

　　事件的编程方式具有轻量级、松榈合、只关注事务点等优势，但是在多个异步任务的场景下，事件与事件之间各自独立，如何协作是一个问题

　　3、回调函数

　　与其他的Web后端编程语言相比，Node除了异步和事件外，回调函数是一大特色。纵观下来，回调函数也是最好的接受异步调用返回数据的方式。但是这种编程方式对于很多习惯同步思路编程的人来说，也许是十分不习惯的。代码的编写顺序与执行顺序并无关系，这对他们可能造成阅读上的障碍。在流程控制方面，因为穿插了异步方法和回调函数，与常规的同步方式相比，变得不那么一目了然了

　　4、单线程

　　JavaScript语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。JavaScript的单线程，与它的用途有关。作为浏览器脚本语言，JavaScript的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定JavaScript同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？所以，为了避免复杂性，从一诞生，JavaScript就是单线程，这已经成了这门语言的核心特征

　　Node保持了JavaScript在浏览器中单线程的特点。而且在Node中，JavaScript与其余线程是无法共享任何状态的。单线程的最大好处是不用像多线程编程那样处处在意状态的同步问题，这里没有死锁的存在，也没有线程上下文交换所带来的性能上的开销

　　同样，单线程也有它自身的弱点，具体有以下3方面：无法利用多核CPU；错误会引起整个应用退出，应用的健壮性值得考验；大量计算占用CPU导致无法继续调用异步I/O

　　像浏览器中JavaScript与UI共用一个线程一样，JavaScript长时间执行会导致UI的渲染和响应被中断。在Node中，长时间的CPU占用也会导致后续的异步I/O发不出调用，已完成的异步I/O的回调函数也会得不到及时执行

　　HTML5定制了Web Workers的标准，Web Workers能够创建工作线程来进行计算，以解决JavaScript大计算阻塞UI渲染的问题。工作线程为了不阻塞主线程，通过消息传递的方式来传递运行结果，这也使得工作线程不能访问到主线程中的UI

　　Node采用了与Web Workers相同的思路来解决单线程中大计算量的问题：child_process。 子进程的出现，意味着Node可以从容地应对单线程在健壮性和无法利用多核CPU方面的问题。通过将计算分发到各个子进程，可以将大量计算分解掉，然后再通过进程之间的事件消息来传递结果，这可以很好地保持应用模型的简单和低依赖。通过Master-Worker的管理方式，也可以很好地管理各个工作进程，以达到更高的健壮性

&nbsp;

### 应用场景

　　在进行技术选型之前，需要了解一项新技术具体适合什么样的场景，毕竟合适的技术用在合适的场景可以起到意想不到的效果。关于Node，探讨得较多的主要有I/O密集型和CPU密集型

　　1、I/O密集型

　　如果将所有的脚本语言拿到一处来评判，那么从单线程的角度来说，Node处理I/O的能力是值得竖起拇指称赞的。通常， 说Node擅长I/O密集型的应用场景基本上是没人反对的。Node面向网络且擅长并行I/O，能够有效地组织起更多的硬件资源，从而提供更多好的服务

　　I/O密集的优势主要在于Node利用事件循环的处理能力，而不是启动每一个线程为每一个请求服务，资源占用极少

　　2、CPU密集型

　　换一个角度，在CPU密集的应用场景中，Node是否能胜任呢？实际上，V8的执行效率是十分高的。单以执行效率来做评判，V8的执行效率是毋庸置疑的

　　CPU密集型应用给Node带来的挑战主要是：由于JavaScript单线程的原因，如果有长时间运行的计算（比如大循环），将会导致CPU时间片不能释放，使得后续I/O无法发起。但是适当调整和分解大型运算任务为多个小任务，使得运算能够适时释放，不阻塞I/O调用的发起，这样既可同时享受到并行异步I/O的好处，又能充分利用CPU
