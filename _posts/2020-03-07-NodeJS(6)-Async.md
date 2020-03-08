---
layout: post
title: NodeJS(六) 异步
date: 2020-03-07 23:53:00 +0800
authorize: Daren
categories: Node
nutshell: Node 中的异步操作
---

在研究如何连接数据库之前, 我们先来看看怎么写异步代码.

## 1. 同步 vs 异步 (Synchronous vs Asynchronous)

我们新建一个项目来展示一个很简单的例子

```javascript
console.log('before');
console.log('after');
```

在执行这两个语句的时候, 是同步的, 阻塞的. 上面的完成之前, 下面的就不会执行.

再来添加一个最简单的异步代码

```javascript
console.log('before');
setTimeout(() => {
    console.log('Time is out');
}, 2000);
console.log('after');
```

`setTimeout`函数的第一个参数是计时结束时要调用的函数,  第二个参数是倒计时的时间(毫秒).

要是按照之前的思路, 很多人会猜这个会像刚刚一样从上到下顺序执行. 但是结果并不是这样.

```bash
heidaren@MacBook-Pro async-demo % node index.js 
before
after
Time is out
heidaren@MacBook-Pro async-demo % 
```

可以发现中间的`setTimeout()`函数反而到了最后才执行. 这是因为`setTimeout()`是异步的方法, 就像之前第一节说过的饭店的例子一样, 程序不会等着条件满足之后才会继续执行, 而是继续执行, 等到`setTimeout()`条件满足之后再执行里面的函数.

**注意**:

异步不意味着同时发生或者多线程, 这里的例子都是一个线程的, 也就是只有一个服务员.

## 2. 异步的编码模式(Patterns for dealing with Asynchronous Code)

现在我们来尝试一下下面的代码:

```javascript
console.log('before');
const user =  getUser(1);
console.log(user);
console.log('after');
function getUser(id){
    setTimeout(() => {
        console.log('Time is out');
        return { id: id, name: 'daren'}
    }, 2000);
}
```

这时如果执行的话, 得到的是这样的结果

```bash
heidaren@MacBook-Pro async-demo % node index.js
before
undefined # 没有被正常返回
after
Time is out
```

可以发现的是`getUser(id)`函数没有等到返回值准备好就已经返回了. 所以输出了`undefined`, 实际上, 在日后使用数据库的过程中, 准备结果所需要的时间是不确定的, 可能瞬间完成, 也有可能要几十秒. 如果直接使用的话, 相关函数也会跟上面一样在结果准备完之前就先行返回了. 因此为了避免像上面代码, Node 有一些编程的模式可以用

- Callbacks 回调
- Promises
- Async/await

## 3. 回调(Callbacks)

回调函数的原理非常简单: 将需要执行的函数作为参数传递进异步函数的运行流程中, 这样的话即使外部的函数提早返回, 也可以在回调函数内的值准备好之后进行下一步操作.

下面用回调函数改写了上面的代码

```javascript
console.log('before');
getUser(1, function(user){
    console.log(user); // 函数作为参数导入
});
console.log('after');
function getUser(id, callback){ // 参数表中写上回调函数
    setTimeout(() => {
        console.log('Time is out');
         callback({ id: id, name: 'daren'}); // 执行回调函数
    }, 2000);
}
```

其实之前用过的 npm 包有的的就是根据回调函数解决异步问题的, 也可以使用省略的箭头函数写法:

```javascript
console.log('before');
getUser(1, function(user){
    console.log(user);
    getRepo(user, (repos) => {
        console.log(repos);
    })
});
console.log('after');
function getUser(id, callback){
    setTimeout(() => {
        console.log('Time is out');
         callback({ id: id, name: 'daren'});
    }, 2000);
}

function getRepo(user, callback){
    setTimeout(() => {
        callback(['repo1', 'repo2', 'repo3']);
    }, 2000);
}
```

```bash
heidaren@MacBook-Pro async-demo % node index.js    
before
after
Time is out
{ id: 1, name: 'daren' }
[ 'repo1', 'repo2', 'repo3' ]
heidaren@MacBook-Pro async-demo % 
```

## 4. 回调地狱(Callback Hell)

刚才我们在一个异步函数`getUser()`中又调用了另一个异步`getRepo()`, 现在我们调用下一个异步`getCommit()`, 这时候代码就变成这样了

```javascript
getUser(1, function(user){
    getRepo(user, (repos[0]) => {
        getCommit(repos, () => {
					// Callback Hell
        });
    });
});
```

而与上面相同作用的代码, 使用同步的方式的话是这样的:

```javascript
const user = getUser(id);
const repos = getRepo(user);
const commit = getCommit(repos[1]);
```

不难看出, 同步写法的话无论是可读性还是代码量上都略胜一筹. 异步函数需要不断嵌套, 显得十分混乱, 在实际的生产中, 这个可能会更糟. 这种情况就被称为“回调地狱”, 或者被称为“圣诞树难题”(看起来像圣诞树).

下面一节就会说明如何避免这种问题.

## 5. 已命名函数 (Named Functions)

如果你想要解决圣诞树难题, 有一种方法就是把所有的匿名回调函数换成有名字的函数, 这样就可以避免圣诞树显得很乱了(我的天我觉得更乱了其实, 这就是在掩耳盗铃啊)

```javascript
console.log('before');
getUser(1, displayUser); // 确实更简洁了, 只用了一行... 但是后面好麻烦的说

function displayUser(user){ // 之前的匿名函数改成有名字的
    getRepo(user, displayRepo);
}
function displayRepo(repos){
    getCommits(repos[0], displayCommit);
}

function displayCommit(commits){
        console.log(commits);
}

console.log('after');
function getUser(id, callback){
    setTimeout(() => {
        console.log('Time is out');
         callback({ id: id, name: 'daren'});
    }, 2000);
}

function getRepo(user, callback){
    setTimeout(() => {
        callback(['repo1', 'repo2', 'repo3']);
    }, 2000);
}

function getCommits(repo, callback){
    callback('commits');
}
```

## 6. Promises 对象

Promises 在英语里的意思是 “保证”, 这里的意思就是说他会保证把异步函数的结果返回. 保持异步操作的最终结果

Promises 对象有三个状态

- pending: 等待状态, 等待相关的异步操作返回内容
- resolve: 得到异步操作的结果
- reject: 记录异步出现错误的信息

例子:

```javascript
const p = new Promise((resolve, result) => { // 建立一个 Promise 对象
    // ...
    setTimeout(() => { // 执行异步语句
        resolve(1);
    }, 2000);
});

p.then(result => console.log('Result ', result)); // 结果
```

```bash
heidaren@MacBook-Pro async-demo % node Promise.js 
Result  1
heidaren@MacBook-Pro async-demo % 
```

还可以查看异步过程中的错误

```javascript
const p = new Promise((resolve, reject) => {
    // ...
    reject(new Error('message'));
});

p
    .then(result => {console.log(result);})
    .catch(err => console.log('Error', err.message)); // 这里使用 dot 来连续访问了 Promise 对象 P
```

Promise 对象的实例有两个函数, `catch` 和 `then`,  前者用来接收异步产生的错误, 后者用来处理异步返回的正常值. 上面的例子中使用了 JS 中的原生 Error 对象. 其中的 `message`属性中存放的就是错误信息.

## 7. 使用 Promises 对象来替换会调函数 (Replacing Callbacks with Promises)

所以刚才的代码可以用 Promises 来改写:

```javascript
console.log('before');

function getUser(id){
    return new Promise((resolve, reject) => {
        console.log('This is getUser');
        setTimeout(() => {
            resolve({ id: id, name: 'daren'});
        }, 2000);
    });

}

function getRepo(user){
    return new Promise((resolve, reject) => {
        console.log('This is getRepo');
        setTimeout(() => {
            resolve(['repo1', 'repo2', 'repo3']);
        }, 2000);
        
    });
}

function getCommits(repo){
    return new Promise((resolve, reject) => {
        console.log('This is getCommits');
        setTimeout(() => {
            resolve('commits is Here');
        }, 2000);
        console.log('after'); // 调整了一下 After 的位置
    })
}
```

## 8. 使用 Promises (Consuming Promises)

 上面用 Promise 改写了回调函数,  而怎么用 Promise 方法也是很有讲究的, 这里有一种好看又简洁的写法:

```javascript
const p = getUser(1);
p
	.then(user => getRepo(user.name)) // then 回返回其中子函数的返回值, 所以这里返回的依然是 Promise 对象
    .then(repos => getCommits(repos[0]))
    .then(commit => console.log(commit))  // 这就是套娃
		.catch(err => console.log('Error', err.message)); // Error Handler, 只要上面三个任何一个 then 出现问题, 这个 catch 就会直接起作用(为什么?)
```

效果:

```bash
heidaren@MacBook-Pro async-demo % node index.js
before
This is getUser
This is getRepo
This is getCommits
after
commits is Here
```

就这样, 我们解决了 Callback Hell 👍

## 9. 创建预定 Promises (Creating Settled Promises)

刚才你已经见识到 Promises 的威力了. 但是现在还有一个问题: 你在进行单元测试的时候, 有时你需要一个已经返回结果的 Promise, 或者一个已经执行失败的 Promise. 这时候应该怎么办呢? 好在 Promise 对象具备应对这种需求的高级 API.

```javascript
const p = Promise.resolve('Hello There');
p
    .then(greet => {console.log(greet)});
```

```bash
heidaren@MacBook-Pro async-demo % node promise-api.js
Hello There
```

注意这里有一个问题就是这个用的不是构造函数, 而是用 Promise 构造器的一个方法来返回一个 Promise 对象. 所以不用 new 运算符

还可以预先定义已经失败的 Promise

```javascript
const p = Promise.reject(new Error('Oops'));
p
    .then(greet => {console.log(greet)})
    .catch(err => console.log(err.message));
```

```bash
heidaren@MacBook-Pro async-demo % node promise-api.js
Oops
```

为什么我们在处理 Promise 的错误时一定要使用 Error 对象呢(这个其实并不强制), 因为 Error 对象会在收集数据的时候返回整个错误对象的调用栈(Calling Stack), 如果你尝试返回整个 Error 对象:

```javascript
const p = Promise.reject(new Error('Oops'));
p
    .then(greet => {console.log(greet)})
    .catch(err => console.log(err));
```

```bash
heidaren@MacBook-Pro async-demo % node promise-api.js
Error: Oops
    at Object.<anonymous> (/Volumes/Workspace/LearnNPM/async-demo/promise-api.js:1:26)
    at Module._compile (internal/modules/cjs/loader.js:1151:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1171:10)
    at Module.load (internal/modules/cjs/loader.js:1000:32)
    at Function.Module._load (internal/modules/cjs/loader.js:899:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:71:12)
    at internal/main/run_main_module.js:17:47
```

如果你只返回一个错误文本的话, 就不会看到整个调用栈(calling stack)了.

## 10. 同时运行 Promises (Parallel Promises)

有的时候你需要几个 Promises 同时运行, 但是如何保证他们都结束之后再进行下一步操作?

```javascript
const p1 = new Promise((resolve) => {
    setTimeout(() => {
        console.log('this is 1');
        resolve(1);
    }, 2000);
});
const p2 = new Promise((resolve) => {
    setTimeout(() => {
        console.log('this is 2');
        resolve(2);
    }, 2000);
});

Promise.all([p1, p2]) // 这里用的这个是 Promise 类的方法, 当中只接受数组
    .then((resolve) => {
        console.log(resolve);
    });
```

```bash
heidaren@MacBook-Pro async-demo % node promise-api.js
this is 1
this is 2
[ 1, 2 ] # 二者结束后归纳到同一个对象中
```

注意这里虽然是多个 Promise 同时执行, 但是依然是一个线程. 也就是同一个线程的异步执行.

如果其中一个出现了 reject 的情况, 整个结果都会被破坏

```javascript
const p1 = new Promise((resolve, reject) => {
    setTimeout(() => {
        console.log('this is 1');
        reject(new Error('Bad thing happend'));
        //resolve(1);
    }, 2000);
});
const p2 = new Promise((resolve) => {
    setTimeout(() => {
        console.log('this is 2');
        resolve(2);
    }, 2000);
});

Promise.all([p1, p2])
    .then((resolve) => {
        console.log(resolve);
    })
    .catch(err => console.log(err.message));
```

```bash
heidaren@MacBook-Pro async-demo % node promise-api.js
this is 1
Bad thing happend
this is 2 # 并没有结果数组被输出
```

如果你想要在两个 Promise 中任意一个首先完成时作出响应, 你需要使用 Promise 类的 race 方法

```javascript
const p1 = new Promise((resolve) => {
    setTimeout(() => {
        console.log('this is 1');
        resolve(1);
    }, 2000);
});
const p2 = new Promise((resolve) => {
    setTimeout(() => {
        console.log('this is 2');
        resolve(2);
    }, 2000);
});
Promise.race([p1, p2])
    .then((resolve) => {
        console.log(resolve);
    })
    .catch(err => console.log(err.message));
```

```javascript
heidaren@MacBook-Pro async-demo % node promise-api.js
this is 1
1 // 第一个 Promise 异步操作执行完后立刻返回结果
this is 2 // 此时第二个 Promise 才执行完.
```

## 11. Async 和 Await

这两个关键字是 ECMAScript 2017 才有的语法糖, 可以用这个来让 Promise 语法更加简洁

```javascript
const p = getUser(1);
p
	.then(user => getRepo(user.name)) // then 回返回其中子函数的返回值, 所以这里返回的依然是 Promise 对象
    .then(repos => getCommits(repos[0]))
    .then(commit => console.log(commit));
```

```javascript
async function Display(){
    const user = await getUser();
    const repo = await getRepo(user.name);
    const commit = await getCommits(repo[0]);
    console.log(commit);
}

Display(); // 在这里执行
```

上下两个代码块的作用是一样的, 但是区别在于下面的 async 可以用类似同步函数和的写法写异步函数.

注意 await 只能在 async 函数中使用.

