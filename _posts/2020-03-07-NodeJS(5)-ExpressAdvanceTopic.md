---
layout: post
title: NodeJS(五) Express 进阶操作
date: 2020-03-07 23:53:00 +0800
authorize: Daren
categories: Node
nutshell: 深入了解 Express
---

## 1. 简介

- 中间件 (Middleware)
- 配置 (Configuration)
- 调试 (Debugging)
- 模板引擎 (Templing Engines)

## 2. 中间件 (Middleware)

后端处理请求就像工厂里繁忙的生产线, 这个生产线被称为 “请求处理管线(Request Processing Pipeline)”, 请求的数据经过处理之后变成 response 返回给客户端.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/Screen%20Shot%202020-02-26%20at%207.47.27%20AM.png)

而中间件本质上是函数, 就好比流水线上的工人, 对数据进行处理之后交给下一个工人.

在之前你已经见过两个中间件(Middleware)了, 之前用的像 `express.json()` 方法, 就可以将 require 发来的 JSON 对象解析到 `req.body` 对象里. 而 express 应用对象的 `use()`方法则用来安装需要的中间件, 之前 `app.use(express.json())`就是这么来的.

还有所有的路径处理器(Router Handler)也是中间件, 他们负责吧收到的请求和要发送的返回放在了回调方法的 req 和 res 对象中.

不只是可以用他自带的中间件, 咱们还可以自己写想要的中间件.

## 3. 写中间件 (Creating Custom Middleware)

可以直接写自己的中间件, 方法也很简单.

中间件本质上就是一个函数, 包含三个参数: 请求, 返回, 下一个中间件的引用.

下面有一个简单的例子:

```javascript
app.use(function (req, res, next) {
  console.log('Logging');
  next();
};
```

在中间件的最后一行一定要调用下一个中间件的引用函数, 不然整个管线都会挂机(hang), 无法返回数据.

比较习惯的做法是将自己的中间件写在单独的文件里, 作为 module 导入.

如果你在

## 4. 内置中间件(Built in Middleware)

express 自带了一堆中间件.

### express.json()

 将 JSON 格式的 request 转换成对象, 然后存到 `req.body` 中.

### express.urlencoded() 

解析旧式的 URL FORM 的请求, 存到`req.body`中, 如果要传递复杂的数组和对象的话, 需要

```javascript
app.use(express.urlencoded( {extended: true}));
```

### express.static()

处理静态的文件请求. 有符合的文件直接把文件返回给浏览器.

```javascript
app.use('static', express.static('public'))
```

`public`指的是在当前目录下的用来存放静态内容的文件夹.

之后就可以直接通过访问`static/public`来访问所需要的内容, 比如我要访问`public`中的`readme.txt`:

```url
http://localhost:3000/static/readme.text
```

**注意**

这里的代码我在 macOS 10.15.3 上配合 node 13.8.0 使用时并不能正常运行, 等到有时间或者用到的时候再仔细研究.

## 5. 第三方中间件 (Thirdparty Middleware)

在 express 官网可以找到很多第三方中间件.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/Screen%20Shot%202020-03-04%20at%209.13.14%20PM.png)

中间件的数量实在是太多了, 这里 Mosh 大佬推荐了两个可能比较常用的中间件:

需要注意的是由于中间键是请求处理管线(request processing pipeline)的一部分, 所以使用中间件会使得处理请求消耗更多的时间, 造成性能降低. 一定要慎重使用中间件, 遵循最简原则.

### 5.1 helmet

helmet 通过设置不同的 http Headers 使得 http 连接更加安全.

#### 安装

```bash
npm i helmet
```

#### 使用

```javascript
const helmet = require('helmet');
app.use(helmet());
```

这里只介绍最基础用法, 详细用法参考官方文档 https://github.com/helmetjs/helmet

### 5.2 morgan

morgan 可以对所有的请求进行 log, 方便调试. 部署之后一般用不到这个, 这东西一般是调试用的.

#### 安装

```bash
npm i morgan
```

#### 使用

```javascript
const morgan = require('morgan');
app.use(morgan(tiny));
```

Morgan 的参数可以用来设置 log 的模式, tiny 是最简记录. 默认是在控制台进行输出, 但是也可以输出到文件

## 6. 开发环境和生产环境  (Envrionments)

实际上, 开发环境(development)和生产环境(production)是有很大区别的, 在开发环境中需要时刻进行 log, 或者其他的一些在部署完成之后并不需要的操作. 为了方便对代码进行管理,  express 提供了通过宿主机的环境变量(environment variable)进行判断是否为开发环境的功能

### 6.1 直接查看 process.env

之前提到过 `process.env` 可以查看当前环境的环境变量, 一般 Node 使用的变量是`NODE_ENV`. 如果你没有设置过, 试图访问的话得到的就是`undefined`.

### 6.2 使用 express 的 `app.get(env)`

使用 `app.get(env)` 与上面不同的是, 如果没有定义 `NODE_ENV`, 那么得到的值就是默认值`development`, 如果你有修改, 那么得到的是当前值.

如果我想在我的`index.js`中只让中间件`morgan()`在开发环境下使用, 可以直接用 `if` 来搞定:

```javascript
if(app.get(env) === 'development') {
    app.use(morgan(tiny));
}
```

现在运行的话, 如果我没有设置 `NODE_ENV` 的话, 直接就就是 development 模式了, 但是如果设置了这个环境变量的话, 这个不会执行

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/Screen%20Shot%202020-03-04%20at%2010.30.42%20PM.png)

将 `NODE_ENV` 的值设为 `production`

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/Screen%20Shot%202020-03-05%20at%207.21.24%20AM.png)

## 7. 配置 (configuration)

实际上, 在开发的时候除了要分别开发环境和生产环境, 还有在两种环境下所需要的配置(configuration), 比如在开发环境下需要连接的数据库不同, 密码也不同. 有一些很好的插件可以帮助存放不同的配置文件,

之前比较流行的配置工具是 npm rc, 但是今天要用的是 config 这个 npm 包

### 7.1 安装

 ```shell
npm i config 
 ```

```javascript
const config = require('config');
```

安装完这个 NPM 包之后, 还需要在项目的根目录下创建一个`config`文件夹, 这个文件夹存放存储相关配置文件的 JSON 文件. 比如

```javascript
heidaren@MacBook-Pro express-demo % tree  
.
├── config
│   ├── custom-environment-variables.json // 这个配置文件和其他配置文件平行, 从当前环境的配置变量中取值
// 为什么要从环境变量中获取值呢, 因为有的时候直接把数据库密码这种东西存到文件中是不保险的, 很容易产生泄漏, 据说
// 美国有一家金融公司就是因为将数据库密码存放饿到文件中, 结果因为代码被版本管理系统挂到网上了, 就泄露被攻击了
│   ├── defult.json // 默认配置, 所有状态都会调用, 但是与其他配置文件重复的值会被覆盖
│   ├── development.json // 当 NODE_ENV 设置为 development 时会调用
│   └── production.json // 当 NODE_ENV 设置为 production 时会调用
```

### 7.2 使用

在 index.js 中加了这几行代码:

```javascript
// Configuration

console.log('Application Name: ' + config.get('name'));
console.log('Mail Server Name: ' + config.get('mail.host'));
console.log('Password: ' + config.get('mail.password'));

```

然后这几个配置文件的内容是这样的:

**default.json**

```javascript
{
    "name": "My Express Application"
}
```

**development.json**

```javascript
{
    "name": "My Express App - Development",
    "mail": {
        "host": "dev-mail-server"
    }
}
```

**production.json**

```javascript
{
    "name": "My Express App - Production",
    "mail": {
        "host": "prod-mail-server"
    }
}
```

**custom-envrionment-variables.json**

```javascript
{
  "mail": {
    	"password" : "app_password"
  }
}
```



现在更改环境变量:

```bash
export NODE_ENV=development # 分别用 development 和 production
export app_password=123456
```

结果:

```bash
heidaren@MacBook-Pro express-demo % node index.js
development
morgan deprecated undefined format: specify a format index.js:10:13
morgan deprecated default format: use combined format index.js:10:13
using morgan
Application Name: My Express App - Development
Mail Server Name: dev-mail-server
Password: 123456
Listening on port 3000
# 上面是 NODE_ENV=development 的情况

heidaren@MacBook-Pro express-demo % export NODE_ENV=production 
heidaren@MacBook-Pro express-demo % node index.js             
production
Application Name: My Express App - Production
Mail Server Name: prod-mail-server
Password: 123456
Listening on port 3000
# 上面是 NODE_ENV=production 的情况
```

## 8. 调试工具 (Debugging)

之前我们要进行调试的话, 都是用 `console.log()`来进行记录, 要是记录完了, 那就注释调或者删掉, 要是出错了就再加这个 `console.log()`, 这么做太不优雅了, 太费劲了, 太难受了, 所以 Node 有专门用来 debug 用的 npm 包, 就叫 `debug`

这个包可以实现这些功能:

- 输出相关信息(代替 `console.log`)
- 可以设置不同命名空间, 根据设置命名空间的不同展示不同命名空间的 debug 信息. 例如数据库可以设置 `app:db`的命名空间, 初始化模块可以设置 `app:startup`来给初始化设置命名空间, 这样可以对代码某个单独的部位进行 debug (注意上面的命名空间可以设置成任意字符串, 那个冒号:只是看起来方便)

### 8.1 安装

```bash
npm i debug
```

```javascript
// 这个理解起来可能有点复杂, 我们两步来看
// 第一步: 
require('debug')('app:startup')
// 调用 require 语句之后返回的是一个函数, 
// 所以我们在后面再添一个括号('app:startup')来执行这个函数到导入进去, 这个参数是用来表示命名空间
// 第二步:
const startupDebugger = require('debug')('app.startup');
// 现在得到的 startDebugger 是另一个函数, 这个函数可以用来 debug
```

### 8.2 使用

要使用这个工具的话, 需要先设置 `DEBUG` 这个环境变量为想要调试的命名空间.

```javascript
const express = require('express');
const morgan = require('morgan');
const config = require('config');
const app = express();
const startupDebugger = require('debug')('app:startup'); // 设置初始化的调试工具
const dbDebugger = require('debug')('app:db'); // 设置数据库的调试工具
console.log(app.get('env'));
app.use(express.json());

if(app.get('env') === 'development') {
    app.use(morgan());
    startupDebugger('using morgan'); // 进行初始化调试
}
// db 
dbDebugger('database debugging...'); // 进行数据库调试

... // 后面代码跟之前一样
```

如果我要对初始化模块进行调试的话, 直接将`DEBUG`环境变量设置为`app:startup`即可

```bash
heidaren@MacBook-Pro express-demo % export DEBUG=app:startup   
heidaren@MacBook-Pro express-demo % node index.js              
development
morgan deprecated undefined format: specify a format index.js:12:13
morgan deprecated default format: use combined format index.js:12:13
  app:startup using morgan +0ms # 这里显示了相关的log
Application Name: My Express App - Development
Mail Server Name: dev-mail-server
Password: 123456
Listening on port 3000
```

如果我把`DEBUG`设置成`app:db`的话是这样的

```bash
heidaren@MacBook-Pro express-demo % node index.js      
development
morgan deprecated undefined format: specify a format index.js:12:13
morgan deprecated default format: use combined format index.js:12:13
  app:db database debugging... +0ms # 显示的是db的log
Application Name: My Express App - Development
Mail Server Name: dev-mail-server
Password: 123456
Listening on port 3000
```

除了这样单独写, 还可以使用通配符, 这样可以同时调试多个命名空间.

```bash
heidaren@MacBook-Pro express-demo % export DEBUG=app:*
```

还可以使用逗号将多个环境变量分隔开

```bash
heidaren@MacBook-Pro express-demo % export DEBUG=app:startup,app:db
```

效果:

```bash
heidaren@MacBook-Pro express-demo % node index.js     
development
morgan deprecated undefined format: specify a format index.js:12:13
morgan deprecated default format: use combined format index.js:12:13
  app:startup using morgan +0ms    # 同时出现
  app:db database debugging... +0ms 
Application Name: My Express App - Development
Mail Server Name: dev-mail-server
Password: 123456
Listening on port 3000
```

## 9. 模板引擎 (Templating Engines)

之前返回的都是 JSON 这样的东西, 但是有的时候你做的项目没有那么的“前后端分离”: 你的后端还是需要返回 HTML 数据的, 那就需要一个像样的模版引擎(Templating Engings)了, 这个好东西可以帮你返回需要的 HTML, 跟 PHP 似的.

对于 Node 来说, 常用的模板引擎有 Pug, Mustache, EJS. 今天介绍的就是 Pug.

Pug 是一个 NPM 包, 所以需要单独安装.

```bash
npm i pug
```

与之前不同的是, 这个包不需要像 module 那样进行导入.

我们需要使用 `app.get()` 函数, 来修改默认的模板引擎(Express 默认的模板引擎是 jade).

```javascript
app.set('view engine', 'pug'); // 修改当前的模板引擎为 pug
app.set('views', './views'); // 存放视图模板. 这个是可选的, 不修改的话默认的目录就是 views
```

然后别忘了建这个存放模板的文件夹

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/Screen%20Shot%202020-03-05%20at%2010.20.37%20PM.png)

之后在这个文件夹下面创建需要的模板文件, 比如我想弄一个首页, 就建一个`index.pug`

```pug
html
    head
        title= title
    body
        h1= message
```

这个东西的写法和 HTML 很不一样, 但是这里就不多说了, 有需要的话看官方文档, 写的很详细

现在要做的就是把这个 pug 文件让服务器渲染成正常的 HTML 文本然后发给客户端.

回到 index.js, 咱们重新建一个 express 的路径处理器, 就选`/`根目录了

```javascript
...
app.get('/', (req, res) => {
    res.render('index', {
        title: 'hello',
        message: 'hello'
    });
});
...
```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/Screen%20Shot%202020-03-05%20at%2010.42.46%20PM.png)

## 10. 数据库整合 (Database Integration)

实际上 Express 可以整合多种数据库, 在官网上详细记录了支持的数据库类型

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/Screen%20Shot%202020-03-05%20at%2010.53.20%20PM.png)

用法大同小异, 无非就是安装相应的数据库驱动, 之后进行连接

后面会有单独的章节讲有关 mongoDB 配合 Node 中的 mongoose 驱动使用的内容. 注意 mongoose 并不是给 express 用的 mongoDB 的驱动 MongoClient, 相比之下 mongoose 的 API 更加简洁.

## 11. 认证 (Authentication)

Express 是否跟其他的请求处理工具一样有认证(Authentication)的能力呢?

并没有, Express 注重轻量化, 并没有认证(Authentication)的概念. 后面会有章节实现认证(Authentication)

## 12. 构建可维护的路径 (Build Maintainable Routes)

实际上之前的项目的结构并不科学, 代码太长, 结构混乱, 难以维护. 理想的状态应该是

- 所有的自定义中间件存放在单独的 middleware 文件夹里
- RouteHandler 分门别类放在 routes 文件夹里, 将请求处理分散到独立的模块中
- 使用 routes 来整合各个模块

这样实现起来跟之前有点不一样, 比如不能单独使用 express, 需要使用 routes 方法返回 routes 对象. 同时不同的组件要在 index.js 中成为中间件.