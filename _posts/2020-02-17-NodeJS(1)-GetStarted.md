---
layout: post
title: NodeJS(一) 开始
date: 2020-02-17 09:20:00 +0800
authorize: Daren
categories: Node
nutshell: NodeJS 简单入门以及概念.
---



## 0. 简介

NodeJS 可以帮助你在本地运行 JavaScript 文件. 说白了就是异步的本地的 js 运行环境. 运行在服务器上

由于 Node 的性能优异, 所以被用来搭建各种后端 API, 是搭建高伸缩性(Highly-scalable), 数据密集型(data-intensive)和实时app(real-time apps) 的理想工具.

- 适合原型设计(prototyping)和敏捷开发(agile development)
- 速度快, 可扩展性高(highly scalable)
- 使用 JavaScript
- 整洁连贯的代码
- 强大的开源生态

##  1. Node 架构 (Node Architecture) 

JavaScript 被广泛用在前端开发领域,  这时 JavaScript 只需要在浏览器中运行, 浏览器中内置的 JavaScript 引擎(engine) 负责把 JavaScript 的脚本语法转换成相应的机器语言.

在以旧版的 Microsoft Edge 浏览器中, 使用的是 Chakra(查克拉) 引擎, 而 Firefox 使用的是 SpiderMonkey 引擎, 在 Chrome 中使用的则是 V8 引擎. 所以在不同的浏览器中, 同样的 JS 代码可能产生不同的行为. 浏览器中同时还提供了 JavaScript 的运行环境(Runtime), 我们经常使用的 window 对象就是运行环境(Runtime)的一部分. 

事实上, 在 2009 年之前, JS 只能在浏览器中运行, 而这时, Node 诞生了. 

Ryan Dahl 将 Google v8 引擎内嵌在了一个名叫 `Node.exe` 的`C++`可执行文件中, 这个程序就是全新的 JavaScript 运行环境(Runtime). 这个运行环境跟浏览器的运行环境不一样, 没有像 `window` 这样的对象, 而且有自己的对象, 比如 `fs` 用来访问文件系统, `http` 用来创建连接等. 这些东西可不能在浏览器里面运行😂.

总而言之, Node 集成了 JS 引擎, 外加运行环境, 包含了一些在浏览器中不能实现的模块.

### Node 不是编程语言

有的同学可能会拿 Node 和 C#, PHP 来对比, 但是 Node 实际上不是编程语言. 

而且 Node 也不应该跟 ASP.NET, J2EE 这些框架(framework) 来表, 因为 Node 根本就不是框架...

**Node 只是执行 JS 语句的运行环境**

## 2. Node 如何工作? (How Node Works?)

刚才提到 Node 具有高伸缩性(Highly-scalable)的特性, 这主要是借助于 Node 不阻断(Non-blocking), 异步(Asynchronous)的特性.

### 什么是异步(Asynchronous)?

可以用服务员来解释这个问题.

假设有一个服务员, 这时来了两桌客人, 那么异步(Asynchronous)的服务员会把第一桌客人的订单先送到厨房, 在厨师准备第一桌的时候帮第二桌点菜, 而不必等到厨师做完第一桌再问第二桌客人.

假如这个服务员是同步(synchronous)的, 那就比较费劲了, 先给第一桌客人点菜, 然后就一直等到厨房做完第一桌的菜, 再给第一桌上菜, 最后再给第二桌点菜, 这时候估计是人家第二桌都饿死了.

所以“非阻断(Non-blocking)”或者叫“异步(Asynchronous)”架构更占优势, “服务员”就是单个 CPU 线程, “客人”对应的就是各种请求. 

而“同步(synchronous)”最大的问题就是他在运行时即使把所有的线程都占满, 而且效果甚至还不如异步. 新的请求只能等待空闲, 两边都不讨好. 像 ASP.NET 这样的框架, 默认就是这么工作的. 要是你想要切换到异步的架构, 那你还要做大量工作. 而 NodeJS 默认就是异步架构, 省去了很多麻烦.

当 NodeJS 收到像查询数据库这种请求时, 不必等待数据库返回数据, 当数据库正在进行处理时, Node JS 会处理接下来的请求. 当数据库最终处理完了数据, 会通过 EventQueue 对Node JS 进行通知. Node 一直在后台监控这个队列, 直到发现有数据更新时, 将数据取出, 再进行处理. 这种异步的处理机制让 Node JS 成为了 I/O 密集型(I/O scalable)应用的理想解决方案. 在服务器算力有限的情况下, 可以处理更多客户端的数据.

但是 Node JS 并不适合来计算密集型(CPU scalable)的应用,  比如视频/照片处理的服务器. 这些吃的主要是 CPU, 而对文件系统和网络压力很小. 而 Node JS 是单线程的, 当处理一个请求的计算时, 其他的请求所需要的计算必须等待. 所以 Node 在这上面不占优势.

