---
layout: post
title: NodeJS(二) 组件(Modules)
date: 2020-02-18 15:01:00 +0800
authorize: Daren
categories: Node
nutshell: NodeJS Module 相关知识.
---



## 1. 全局对象 (Global Object)

```javascript
console.log(); // Global Object
```

我们之前常用的 `console.log()` 函数可以在任何位置被访问, 不管是函数外还是函数内, 像这种在任何位置都能进行访问的对象, 这些对象是全局作用域(Global Scope)的一部分,   叫做全局对象(Global Object).

```javascript
# 在 JavaScript 中有这些常用的全局对象
console.log();
setTimeout(); // 用来设置暂停
clearTimeout(); // 用来清除暂停

setInterval(); // 用来设置间隔来重复执行函数
clearInterval(); // 取消重复执行
// 在浏览器中的 window 对象代表了整个全局作用域.
// 所有的全局对象前面都可以加一个 window. 因为这些对象就定义在 window 里面
window.console.log();
window.setTimeout();
var message = ''; // 由于声明的是全局的, 也可以用window 访问
window.message;
```

但是在 Node 中, 并没有和 `window`对象, 取而代之的是 `global` 对象.

```javascript
global.setTimeout();
global.console.log();
console.log();
```

另一个不同的地方是, 变量并没有被定义在`global`中, 而是被定义在了当前写代码的的 .js 文件中, 比如我例子里的是 `app.js` , 这是由于 Node 的模块系统(Module System).

## 2. 模块 (Modules)

上一节说到, 在浏览器中创建的所有的全局变量, 最终都是定义在 JS 运行环境中的`window` 对象中, 但是这里有一个显而易见的问题: 在复杂的开发中, 需要将一个网站的 JS 文件分解成多个 JS 文件, 由于 `window` 的存在, 我们无法在不同的文件中使用相同的对象名或者变量名, 否则`window`中的对象就会被覆盖, 这就比较麻烦了. 这就是为什么, 在最新的 ES6 标准中, 出现了 `let` 来定义块作用域的变量, 而没有人推荐你再使用 `var` 来声明全局变量了.

在 NodeJS 中, 为了避免这个问题, 出现了模块化的概念(Modularity), 你在独立文件中声明的全局变量, 并不会上升到 `global` 对象中, 而是在当前 JS 文件的作用域中, 限制在当前模块(Module)中. 这就避免了对象被覆盖的问题.

在 Node 中的每一个文件都被视为一个模块(Module), 定义在这个文件里的变量或者对象都被限制在文件作用域中, 并且是不能从外部访问的私有变量和私有对象, 模块之间都是彼此隔离的. 如果要从外部访问, 需要特别操作.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/2-0-modules.png)

而在 NodeJS 中, 需要用一个模块(Module)来调起其他的模块, 这个模块被称为“主模块(Main Module)”. 可以类比 C 语言中用来调起其他函数的“主函数(Main Function)”.

模块的相关信息都存储在自身的 `module` 对象中, 可以用 `console.log()` 来输出.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/2-1-module-object.png)

```shell
MacBook-Pro:NodeJS daren$ node app.js
Module {
  id: '.', # 每个模块都有唯一ID 作为识别标识
  exports: {}, # 用来导出某些对象
  parent: null,
  filename: '/Users/daren/Desktop/NodeJS/app.js', # 文件的完整路径
  loaded: false, # 决定是否加载
  children: [],
  paths:
   [ '/Users/daren/Desktop/NodeJS/node_modules',
     '/Users/daren/Desktop/node_modules',
     '/Users/daren/node_modules',
     '/Users/node_modules',
     '/node_modules' ] } 
```

 ## 3. 新建模块 (Creating Module)

假设我们有了一个 logger 模块, 用来记录 Node 项目用来运行时的某些信息. 

```javascript
var url = 'http://mylogger.io/log';
function log(message) {
  // Pretending Send HTTP request
  console.log(message);
}
```

但是这么一个模块里面的都是私有对象, 怎么被主模块导入呢?

这就需要用到刚才在`module`对象中看到的 `export` 对象了, 这个对象的作用就是把某些内容导出. 直接修改这个`module.export`对象即可.

```javascript
...
module.exports.log = log;
...

```

或者你可以直接把一个对象引用过去...

```javascript
module.exports = {
  log: log
}
```

本质上, Module 之间的传值, 就是 JSON.

这么做的目的, 就是通过 Module 的封装将整个 JS 文件抽象化, 你可以直接使用而不必研究内在的逻辑. 好比一台 DVD 播放机, 你只需要按下按钮就可以播放, 而不需要搞懂里面的所有部件. Module 就是 DVD 播放机, export 就是按钮, 是播放机的公共接口.

## 4. 加载模块 (Load Modules)

在 exports 模块的某些内容之后, 我们还需要主模块中对其他模块导出的内容进行导入. 这里要用到的是`require()`方法, 这个方法只有一个参数, 那就是需要导入的模块的路径, 而执行完成之后的返回值是一个对象, 直接可以赋值给其他对象.

**app.js**

```javascript
var logger = require('./logger.js');
var logger = require('./logger'); // 这样也可以
logger.log('Hello');
...
```

如果你的 Module 值 export 了一个方法, 你甚至可以直接将方法赋值给`module.export`, 因为函数本身也是一种对象.这么做的好处是, 你不必再通过 dot notation 就能直接访问到方法.

另外一个需要注意的地方就是, 为了避免导入的对象或者变量被不小心覆盖掉, 最好把 `require()`赋值给 `const` 类型的静态变量, 防止被修改.

**logger.js**

```javascript
...
module.exports = log;
...
```

**app.js**

```javascript
const log = require('./logger.js')
...
```

## 5. 模块打包函数 (Module Wrapper Function)

每个 Module 内部的变量都是私有的, 并且无法从外部进行访问. 打包函数与每个 Module 的私有化实现有关.

每个 JS 文件中写的内容, 在通过 Node 执行时, 都会被自动包(Wrapper) 进一个打包函数中.

```javascript
(function (exports, require, module, __filename, __dirname) {
  
});
// 以上为打包函数,
/*
	其中的参数:
	exports: module.exports 的引用, 所以直接访问 exports 和访问 modules.exports 是等效的.
	由于这是 module.exports 的引用, 所以不能跟刚才一样直接把要导出的函数引用给他, 不然就乱套了😂.
	
	require: 之前我们用来把 exports 赋值的函数. 
	你可能以为这个是属于global对象的全局函数, 其实它还是局部的.
	每个 Module 的 require() 都是作为打包函数参数被导入的本地函数.
	module: 就是之前用过的 module 对象.
	
	__filename: 包含完整绝对路径的文件名
	__dirname: 完整绝对路径 
	
*/
```

在运行时(Runtime), 我们的代码会变成这个样子:

```javascript
(function (exports, require, module, __filename, __dirname) {
	var url = 'http://mylogger/log';
	function log(message) {
    return message;
	}
});
```

实际的代码还要比这个复杂一点, 如果你是 JS 大佬, 可能会看出来这个写法是“立即执行函数表达式(Immediately-Invoked-Function-Expression aka.IIFE)”.

所以 Node 并不是直接执行我们的代码, 代码总是被包裹在打包函数里, 由于这个是匿名函数, 所以里面的变量都变成私有的了.

同时呢, 除了你自己写的 Module 呢, Node JS 自带了很多很牛逼的 Module. 都写在官方的 Doc 里. 

## 6. 路径模块(Path Module)

这里拿 Path Module 来举个例子. 首先我们从官网的 Doc 查到了这个 Module 有一个 parse 方法:

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/6-1-path-parse-module.png)

然后我们导入这个 module

```javascript
const path = require('path');
// 注意这里导入自带 module 的方式是直接写上 module 的名字, 而不是路径, 当找不到自带的 module 时, Node 会查找相关的文件
// 如果你要导入项目中的同名 module , 可以直接导入这么写:
const path = require('./path');
```

之后就可以直接使用这个函数了:

**app.js**

```javascript
const path = require('path');
console.log(path.parse(__filename));
```

**bash**

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/6-2-path-parse-inBash.png)

另外, 这些自带的 module 也是非常重要的:

- File System: 和文件系统进行交互
- HTTP: 用来搭建简单的 HTTP 服务
- OS: 用来与系统交互
- Path: 用来操作路径
- Process: 与进程相关的 module
- Query Strings: 处理队列, 在 HTTP 服务器中会用到 
- Stream: 流处理

## 7. OS 模块(OS Module)

OS 模块能实现的特性, 是运行在浏览器中的 JS 绝对不能企及的, 借助 OS 模块, 可以方便的与操作系统进行交互.

**app.js**

```javascript
const os = require('os');
var totalMemory = os.totalmem();
var freeMemory = os.freemem();

console.log('Total Memory: ' + totalMemory);
```

**bash**

```bash
MacBook-Pro:NodeJS daren$ node app.js
Total Memory: 17179869184
MacBook-Pro:NodeJS daren$ 
```

## 8. 文件系统模块(File System Module)

文件系统模块主要用来使用各种文件, 用来和文件系统进行交互. 在使用的时候, 很多方法都是成双出现的

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/7-1-fs-object-list.png)

其中以 `sync` 结尾的就是同步(synchronous), 阻断(block) 类型的方法, 而没有 `sync` 结尾的就是非同步(asynchronous)类型的方法. 

虽然参数简单, 但是注意要谨慎使用同步方法, 因为 node js 是单线程执行的, synchronous 类型的方法会放大时间开销.

而异步版本的方法都需要回调函数(callback), 这个函数将在相应的异步方法执行完成后执行.

```javascript
const fs = require('fs');
var dir = fs.readdirSync('./');
var dirAsynchronous = fs.readdir('./', function(err, file){
  if(err) console.log(err);
  else console.log(file);
})
```

这里有一个坑就是, 异步的函数没有返回值, 但是可以通过回调函数进行赋值的操作. 而具体回调函数需要哪些参数, 还是要依据文档为准.  

## 9. 事件组件 (Events Module)

Node 中有一个十分重要的概念就是**事件**(Event). 实际上很多 Node 的核心功能都是基于事件来实现的.

简而言之, 事件(Event)就是一件事情发生的信号. 

举个例子, 在 Node 中, 我们有一个 HTTP 类, 用来建立 web 服务, 所以我们需要监听某个端口(port), 而每次我们接收到请求的时候, HTTP 类机会产生新的事件(Event), 而我们要做的就是对这个事件进行响应(Response), 这就要读取这个请求, 然后返回.

所以这里就要介绍一下如何使用事件模块(Events Module).

*注意: object 是 class 的实例*

 要使用事件, 有一个很重要的类叫做 Event Emitter, 里面提供了很多和事件相关的方法, 在官网的 Events 相关的 Doc 中能够查到.

虽然 EventEmitter 里面有一堆方法, 但是常用的据说就下面两个.

```javascript
const EventEmitter = require('events'); 
// 注意命名规范, EventEmitter 首字母大写表示这个是类而不是对象
// class 里面有 property 和 method
const emitter = new EventEmitter();
// object 是 class 的实例
// EventEmitter 定义了 Event Emitter 都能做什么, 而 object 就是 EventEmitter 的实例

// 下面要注册一个 Listener, Register a listener
// on 方法接受两个参数, 第一个是事件的名字, 第二个是回调函数, 也就是接收到事件的进一步行为.
// 要注意 Listener 一定要在 Emitter 之前, 不然 event 产生之后才进行监控就什么都没有了.
emitter.on('messageLogged', function(){
    console.log('Listener called');
});

// 下面注册的就是 Emitter
emitter.emit('messageLogged'); // Raise an event, 这里产生了一个事件
// emit means Making a noise, produce - signalling
// 直白的说这个 emit 就是用来下产生 event 的, 里面的参数是 event 的名字
```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/10-1-1-emiWithMessage.png)

当 emitter 进行时, 会在所有已经注册的 listener 中进行遍历比对, 这个是同步执行的(synchronously). 所以如果 listener 在执行 emitter 之前还没有注册的话, 肯定是不能用的.

## 10. 事件参数 (Event Argument)

**看见参数用什么单词吗? Argument, 这个词专门指实参, 而 parameter 用于形式参数(形参), 有关编程的语言规范,请看http://www.open-std.org/jtc1/sc22/wg14/www/docs/n1124.pdf**

当你发送某些事件的时候, 你可能希望同时包含这个事件的一些附加信息. 比如给你的事件加一个 ID, 或者带一个 URL 等等, 这就需要给 emitter 和 listener 添加参数了

```javascript
const EventEmitter = require('events'); 
const emitter = new EventEmitter();
emitter.on('messageLogged', function(args){ // 这里的 args 导入的就是 emitter 的第二个参数
    console.log('Listener called', args);
});

emitter.emit('messageLogged', {id: 1, url: 'http://'}); 
// emitter 的参数你可以单独写, 但是最好放在同一个对象里更方便些, 具体分开怎么写暂时我也不知道😂
```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/10-1-2.png)

```javascript
emitter.on('messageLogged', (args)=>{ 
    console.log('Listener called', args);
}); // 你也可以用 ES6 里的 箭头匿名函数进行改写 Arrow method
```

## 11. 继承 Event Emitter (Extending EventEmitter)

在实际的情况中, 很少直接使用 EventEmitter 更多的是将这个打包进了一整个新的 class 中. 这个比较难理解, 因为如果你在一个 Module 里用 Emitter, 另一个 Module 里用 Listener, 就接受不到了, 因为 Listener 只能接收跟他来自同一个 EventEmitter 对象的 Emitter, 两个 Module 之间两个 EventEmitter 显然就不行了.

为了解决这种情况, 一般的方式就是写一个类单独来继承 EventEmitter 类, 同时包含调用 emit() 的方法, 然后跑到想要用 Listener 的地方新建实例.

**app.js**

```javascript
const Logger = require(./logger);
var logger = new Logger();
logger.on('messageLogged', (arg) => {
  console.log('Listener called', arg);
});
```

**logger.js**

```javascript
const EventEmitter = require('events');
class Logger extends {
  log(message){
    console.log(message);
    this.emit('messageLogged' + {id: 1, url: 'http://'});
  }
}
```

## 12. HTTP 模块 (HTTP Module)

这个模块用来建立HTTP连接

```javascript
const http = require('http');
const server = http.creatServer();
server.on('connection', (socket) => { // connection 参数 在 DOC 里面有  
  console.log('connection Established');
})
server.listen(3000);
console.log("Listening port 3000");
```

因为 http.Server 继承了 net.Server, 而 net.Server 本身就是一种 EventEmitter, 所以这里建立的 server 可以用 on, emit 这种方法, 但是这种方法也太底层了, 我们一般不会用到.

```javascript
const http = require('http');
const server = http.creatServer((req, res) => {
  if(req.url === '/'){
    res.write('Helloworld');
   	res.end();
  }
  if(req.url === '/api'){
    res.write('Helloworld');
    res.end();
  }
});
server.on('connection', (socket) => {
  console.log('connection Established');
})
server.listen(3000);
console.log("Listening port 3000");
```

所以可以简要概括成下面这几个动作:

- 导入Modules
- 创建 server
- 创建 Listener / 监听方法
- 进行反馈

而这样来进行路由显然费力不讨好, 网站太多根本就忙不过来. 所以我们也不会用这样的方式处理请求, 而是用 Express 框架.