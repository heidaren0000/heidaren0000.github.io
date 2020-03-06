---
layout: post
title: NodeJS(四) Express
date: 2020-03-05 23:53:00 +0800
authorize: Daren
categories: Node
nutshell: 强大的 NodeJS Web 应用框架
---

## 1. 简介

之前用 http 模块的 server 中的回调函数, 可以简单的对 http 请求作出回复, 但是那里面的 if 也太多了, 不实用.

这节要讲的 Express 就会解决大部分问题.

## 2. RESTful 服务 (RESTful Services)

RESTful 服务也被称为 RESTful API , 这是一种 API 的风格. 经常用于客户端和服务端的交互. 

REST 的全拼是 `RepresentationalStateTransfer`, 因为这个东西是博士生提出来的, 所看起来很高大上, 实际也就那样呗.

客户端(前端)与服务器的交互, 一般就是通过`HTTP`协议(Protocol)来进行的. 一共有四个操作: CRUD(Create, Read, Update, Delete), 也就是增删改查, 而与之对应的 HTTP 请求动作, 分别就是 `POST`, `DELETE`, `PUT`, `GET`, 你想要实现相关的功能, 就可以使用相关的动作.

一般情况下, RESTful 风格的 API 都是在专门的域名或者子域名下的, 方便调用. 而不同的 API 也有不同的地址.

简而言之, RESTful 就是使用简单, 有意义的地址来作为 API



## 3. 介绍 Express (Introducing Express)

Express 的目的主要是为了解决 http module 中使用回调函数来处理 http 请求过于复杂的问题. 

在 Node 社区中, Express 被大量使用, 每周都有超过一千万次下载, 这个包轻量, 快速, 而且又很完善的文档.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/2-1-download-count.png)

要安装 Express, 使用

```shell
npm i express
```

## 4. 建立第一个服务器(Building Your First Web Server)

当导入 require('express') 时, 导入的是一个函数, 而这个函数会返回 express 对象, 一般被称作 express 应用程序, 这面还有一些简单的方法, 像 `app.get()`, `app.post()`, `app.put()`, `app.delete()` 等等, 对应相应的 HTTP 方法

 ```javascript
const express = require('express'); // 这里导入的是函数
const app = express();

app.get('/', (req, res) => { // route
    res.send('<title>Helloworld</title>Helloworld'); // route handler
});
app.listen('3000', () => console.log('Listening on port 3000'));

 ```

其中的`get()`方法的回调方法中的 req 和 res 都是 express 的对象 `request` 和 `reponse`, 这两个对象的属性和方法可以直接在官网查到. 每一个`get()`的代码块被称为一个路径(route), 其中的回调函数被称为路径处理器(router handler).

还有一点是这里并没有用 if-else 来处理选择情况, 而是使用多个 get() 方法来设置 routes, 随着写不同的模块, 这些 routs 可以被移动到别的位置.

`app.listen()`方法中的两个参数为端口和回调函数.

这些 router 在大项目项目中会被分开放在不同的 module 里面, 还要和数据库进行交互.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/4-4-1%20setHelloworld.png)

## 5. Nodemon

前面我们使用 node 运行 js 文件的时候, 假如你对某个文件作出了修改, 那么你就得重启 node 才能看到效果. 如果你用过 Jekyll, 你会发现修改 Jekyll 的文章之后不需要在命令行里重启 Jekyll 直接就能看到. 

在 node 中也有这样的插件Nodemon, 这是个在 CLI 里面运行的插件, 所以要安装到全局.

```shell
node i -g nodemon
```

装好之后, 直接用`nodemon`来代替之前的`node`即可

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/4-5-1-use-nodemon.png)

现在 nodemon 就会根据你的修改实时进行刷新, 不再需要重启 node 就可以看到修改的效果了.

## 6. 环境变量 (Environment Variables)

上个例子里我们把监听的端口设置成了 3000, 但是在生产环境中这个端口不一定是 3000, 比如别的服务已经占用 3000 端口了, 那么这个端口就必须随机应变. 问题来了: 难道到时候再翻代码找到这个语句吗? 太费劲了吧... 一个比较好的方法就是使用环境变量(Environment Variables).

顾名思义, 环境变量(Environment Variables)并不是来自程序代码里的变量,  而是来自程序外部的运行环境(Host Environment), 从运行环境中获取的环境变量. 在配置 JDK 的时候那个环境变量, 就是这个环境变量.

要在程序中读取环境变量, 我们可以使用 node 自带的对象 `process`.

访问 `process.env.[变量名]` 即可访问相关的环境变量.

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('<title>Helloworld</title>Helloworld');
    //res.send('Helloworld');
})

const port = process.env.PORT || 3000;
// 这里如果访问没有 PORT 这个环境变量的话默认使用 3000 端口.
app.listen(3000, () => {
    console.log(`Listening on port ${ port }`);
  // 用 backtic 代替了 single quote, 方便使用模版字符串
})

```

现在, 如果你的电脑里面 `PORT` 这个环境变量的话, 他就会自动使用环境变量作为监听端口. 如果没有这个变量也不至于炸, 还有 3000 端口在后面顶着呢.

怎么给自己添加环境变量呢? 这里介绍一下怎么给 Linux 和 macOS 来设置环境变量(个人认为给 Windows 这样的系统上跑 node 有点不现实...).

**要临时设置环境变量**, 可以使用`export`命令.

```bash
export PORT = 5000
```

按下回车, 你的环境变量就多了一个值为 5000 的 PORT 变量.

注意这个变量只能在当前回话(session)有效, 如果你把这个 terminal 窗口关上了, 就不能用了. 

**要给当前用户设置环境变量**, 可以编辑当前用户目录下的终端配置文件, 这里不同的终端不一样, bash 的配置文件是当前用户目录下的 `.bash_profile`, 在里面添加 export 语句即可

**给所有用户设置环境变量**, 这个可以直接编辑`/etc/profile`, 在最后一行添加 export 语句即可.

## 7. 路由参数 (Router Parameters)

对于复杂的网站不可能对每一个页面都做一个 router, 这时候可以用路由参数(Router Parameter) 来提高代码的复用性.

```javascript
app.get('/param/:name/:say', (req, res) => { // 定义了两个参数 name 和 say
    res.send(req.params);
})
```

要使用 get 方法的 router parameter,  需要在参数名前面加一个 `:`,  比如上面的 `:name`, `:say`, 而参数会以 `params` 对象的形式传入函数内, 可以用 `res.send()` 方法返回.

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/4-7-1-returntopage.png)

路由参数用来访问的是资源, 如果你想向服务器发送信息, 还可以使用 查询参数(Query Parameter), 这个参数可以从浏览器像服务器传递变量. 

使用查询参数不需要在 node 中单独进行规定, 只需要将 `?` 分隔符接在 URL 的后面加上`[变量名]=[值]]`

```url
http://localhost:3000/param/wooh/hello?Set=123
```

跟路由参数类似, 之后这个值会被传入回调方法中的 query 对象中, 直接访问即可.

```javascript
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('<title>Helloworld</title>Helloworld');
    //res.send('Helloworld');
})
app.get('/param/:name/:say', (req, res) => {

    res.send(req.query);
})

const port = process.env.PORT || 3000
app.listen(3000, () => {
    console.log(`Listening on port ${ port }`);
})
```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/4-7-2-query-parameter.png)

## 8. 处理 GET 请求 (Handle GET Request)

之前的代码已经能实现简单的 GET 了, 但是没有说明在找不到资源的情况

```javascript
const express = require('express');
const app = express();

const data = [
    {id : 1, name: 'daren'},
    {id : 2, name: 'hei'},
    {id : 3, name: 'jhon'}
];

app.get('/api/:id', (req, res) => {
    const dat = data.find( d => d.id === parseInt(req.params.id));
  	// 返回 404
    if(!dat) res.status(404).send('Can not find resources');
    res.send(dat);
})


const port = process.env.PORT || 3000;
app.listen(3000, () => {
    console.log(`Listening on port ${ port }`);
})

```

## 9. 处理 POST 请求 (Handle Post Request)

 ```javascript
app.post('/api', (req, res) => {
    const newData = {
        id: data.length + 1,
        name: req.body.name // 注意这里需要使用 中间件(middleWare)
    }
    data.push(newData);
    res.send(course);
})
 ```

跟上面 GET 类似. 但是要让 express 正常解析 JSON 格式, 需要使用 json 中间件(MiddleWare) 对数据进一步处理.

```javascript
app.use(express.json());
```

## 10. 使用 Postman 调试终端 (Calling Endpoints Using Postman)

## 11. 输入检验 (Input Validation)

为了安全起见, 不能过分的信任服务端发来的数据, 我们要进行输入验证(Input Validation), 筛选出符合规范的内容, 过滤掉不符合标准的内容.

```javascript
app.post('/api', (req, res) => {
    if(req.body.name.length < 3 || !req.body.name) {
        res.status(400).send('Bad Request');
        return; // 防止下面的函数被继续执行
    }
    const newData = {
        id: data.length + 1,
        name: req.body.name
    }
    data.push(newData);
    res.send(course);
})
```

这是最简单的验证, 但是在某些情况下服务器要进行的检验比这复杂的多. 为了应对更加复杂的情况, 可以使用专用的模式(schema)检验工具 `JOI` 进行校验. 注意由于 require() 得到的 JOI 是一个类, 所以首字母大写.

```shell
npm -i joi
```

```javascript
app.post('/api', (req, res) => {
    const schema = {
        name: Joi.string().min(2).required
    };
    const result = Joi.validate(req.body, schema);
  	if (result.error) return res.status(400).send(result.error.details[0].message);
    const newData = {
        id: data.length + 1,
        name: req.body.name
    }
    data.push(newData);
    res.send(course);
})

```

在正确的情况下, JOI 返回的对象中 Error 是空白的, Value 是有值的, 而当出错的时候 Value 是空白的, Error 是有值的.

## 12. 处理PUT请求 (Handling HTTP PUT Requests)

要处理 PUT 请求, 需要:

- 查找这个资源 (404)
- 检验这个资源是否符合要求 (400)
- 更新资源
- 返回结果

由于 POST 和 PUT 都需要校验这个资源是否符合要求, 通常都是把校验单独拿出来放在一个函数里.

```javascript
const checkVaild = function(data){
    const schema = {
        name: Joi.string().min(2).min.required
    };
    const result = Joi.validate(data, schema);
    return result;

}
```

然后直接使用 put 方法

```javascript
app.put('/api/put/:id', (req, res) => { // 使用 id 型参来查找资源是否存在
    const dat = data.find( a => a.id === req.params.id );
    if(!dat) return res.status(404).send('resources not found');
    const { error } = checkVaild(req.body); // 校验
    if(error) return res.status(400).send( error.details[0].message );    }
    dat.name = req.body.name; // 修改数据
  	res.send(dat); // 返回修改后的数据
});
```



## 13. 处理 DELETE 指令(Handle Delete Request)

- 查找资源是否存在 (404)
- 删除
- 返回结果

```javascript
app.delete('/api/courses/:id', (req, res) => {
    const dat = data.find( a => a.id = req.params.id );
    if(!dat) res.status(404).send('Resources not found');

    const indes = data.indexOf(dat);
    data.splice(index, 1);

    res.send(course);
});
```

## 备注

因为之前没有下好 POSTMAN,  没法调试前面的代码, 所以又把代码重写了一遍放在这里, 使用了这一节讲到的所有内容.

```javascript
const express = require('express');
const app = new express();
app.use(express.json());

const Joi = require('joi');

const schema = Joi.object({
    name: Joi.string().min(2).max(20).required()
});

var data = [{
    id : 1,
    name : "daren"
}]

app.get('/api/getinfo', (req, res) => {
    res.send(data);
});

app.post('/api/post', (req, res) => {
    const result = Joi.validate(req.body, schema);
    data.push({
        "id" : data.length + 1,
        "name" : result.value.name
    });
    res.send(data);
    console.log(data);
});

app.put('/api/update/:id', (req,res) => {
    const result = Joi.validate(req.body, schema); 
    const target = data.find((a) => { if(a.id === result.params.id) return a; });
    if(!target) return console.log('Error 400 Not found resource');
    target.name = result.value.to;
    console.log(data);
    res.send(data);
});

app.delete('/api/delete/:id', (req, res) => {
    console.log(req.params.id);
    if(!data[req.params.id - 1]) return res.status(400).send("Error: Resources not found!");
    data.splice(req.query.id, 1);
    res.send(data);
})

app.listen(3000, () => {
    console.log("Listening on port 3000");
});
```

