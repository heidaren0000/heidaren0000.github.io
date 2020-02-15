---
layout: post
title: JavaScript 基础
date: 2020-01-30 09:48:00 +0800
authorize: Daren
categories: Code
nutshell: 几十分钟掌握JS基础
---

## 前言

这是在 youtube 上看 freecodeorg 的 JavaScript 速通教程, 做的笔记, 如果有什么翻译错误的地方, 还希望大家多多指教. 由于这个教程比较短, 有一些东西没有讲, 我会在后面找时间补全. 比如

- JS 与 DOM 的交互

还有些地方我觉得太冷门, 就跳过了

在 console 输出文本:

```html
<script>
	console.log("Helloworld"); // 这个可以用来在 从 console 进行输出
</script>
```

## comments 注释

```javascript
var nuvvmber = 5; // in-line comment 单行
/* 多行注释
*/
number = 9;
```

## DataTypes 数据类型

JavaScript 提供这些数据类型: undefined, null, boolean, string, symbol, number, object

常用的咱们都明白, 那么咱们看看不常见的

### undefined

undefined 用来说明这个变量还没有被设定成其他其他的数据类型, 后米可以在改成其他的类型

### null: noting

### object

用来存储各种键值对, 后面会用到

## 声明变量

`var myName = "Daren"`

`let ourName = "TurboMagic"`

`const pi = 3.14 ` 

const 的变量是只读常量, 也就是不能改变这个变量值, 否则会报错, 一般用大写命名, 要注意的是数组(Array)和对象(Object)中的静态元素可以使用括号记法(bracket notation)来重新赋值(引用来形容更准确), 如果要绝对禁止被改动的话, 可以使用 `Object.freeze(想要冻结的对象)`来阻止这个对象被更改.

### let 和 var 的区别

- `let` 是只有块作用域的局部变量, 特别适合给 for 来当参数用, 而 `var` 则是具有全局变量

- `let` 声明的变量都不是window 对象, 不能使用 `window.变量名` 来进行访问
- `var` 重载会覆盖后面所有的变量, 而`let`重载只会会在块作用域中进行
- `var` 声明的变量会被提升到顶端, 也就是在声明变量之前就可以使用这个变量, 但是 `let`不会被提升, 在`let`变量声明语句之前都不能使用`let`变量, 这个也被称为`暂时性死区`

### 共同点

- 在函数内声明, 他们都有`函数作用域`
- 再区块外声明, 他们都有`全局作用域`

**注：**

  允许在程序的任何位置使用 var 重新声明 JavaScript 变量

  在相同的作用域，或在相同的块中，通过 let 重新声明一个 var 变量是不允许的

  在相同的作用域，或在相同的块中，通过 let 重新声明一个 let 变量是不允许的

  在相同的作用域，或在相同的块中，通过 var 重新声明一个 let 变量是不允许的

  在不同的作用域或块中，通过 let 重新声明变量是允许的

https://www.cnblogs.com/wjrelax/p/11238278.html

## 赋值运算符 assignment operator

semicolons 分号

```javascript
var a; // 声明一个变量 a
var b = 2; // 声明一个变量 b 并赋值一个 2
a = 7;
b = a;
```

实际上, deceleration 和 initialization 是完全两回事, 很多人将他们混为一谈,

```javascript
var b = 2; // 这个行为实际上就是 首先声明 一个名字叫 b 的变量, 然后进行初始化, 将 2 赋值到 b 上.
```

### Case Sensitivity in Variables 大小写敏感

js 中 变量名和 函数名 大小写敏感

## Decimal Numbers 小数表示(浮点数)

可以直接写小数

## 运算符 

除了我们常见的加减(subtraction)乘(multiplayer)除(divide)取余(remainder)运算, JS 中还有各种复合运算符(Compound  Assignment),  自增和自减也是和C语言一样的

### Augmented 复合运算符

+=, -=, *=, /= 

### 比较运算

注意比较运算符有两种, 一种 `==` 另一种`===`, 分别对应抽象相等和严格相等(Strict Equality Operator)

`===`严格相等指的是无论变量值和变量类型都是相等的

`==`抽象相等指的是在比较时首先会进行强制类型转换, 之后才比较值是否相等

```javascript
3 == '3'; // ture: 经过类型转换之后两者相等
3 === '3'; // false: 二者变量类型不同
```

除了 equality operator,  还有 inequality operator, 就是`!=`, 而 strict equality operator 对应的反面就是`!==` 

## 字符串

### 转义字符 escape character

```javascript
var myStr = "I am a \"double quoted\" string inside \"double quotes\"";
// 另一种在字符串中使用双引号的方式是把外面的双引号换成单引号
var myStr = 'I am a "double quted" string inside double quotes';
```

 常用的 escape character  

```javascript
/* 
CODE OUTPUT
\' single quote
\" double quote
\\ backslash
\n new line
\r carriage return
\t tab
\b backspace // how to use that 0100
\f form feed
*/
```

### 使用`+`连接两个字符串 Concatenating Strings with Plus Operator

```javascript
var outStr = "I come first" + "I come second";
// 甚至可以使用 += 来对字符串进行连接
var outStr = "This is the first String";
outStr += "This is the second String";
console.log(outStr);
```

### 在字符串中加入变量

这个也可以用`+` 非常随意.

```javascript
var outName = "9527";
var outStr = "Hello iam " + outName + ", what about you?";
// 也可以使用 += 在字符串后面追加变量
outStr += outName;
console.log(outStr);
```

### 获取字符串长度

这个可以通过访问相关字符串的`.length`属性来返回字符串长度

```javascript
var outName = "9527";
console.log(outName.length);
```

### 括号记法(Bracket Notation)来查找这个字符串的第一个字符

就是可以使用类似访问 C 数组元素的方式来对字符串的每个字符进行访问.

```javascript
var myStr = "Helloworld";
var firstLetter = myStr[0];
```

### 字符串不变性 Sting Immutability

```javascript
var myStr = "Jelloworld"
// 如果你想把开头的 "J" 换成 "H" 由于字符串不变性, 下面的方法行不通
myStr[0] = H; // 字符串不能被单独修改
// 正确的方法是把整个字符串整个重新赋值
var myStr = "Helloworld";
```

## 数组

### 基本用法

可以拿来存储不同类型的变量, 注意数组名和数组是引用关系

```javascript
var ourArray = ["John", 23];
// 这个比较随意, 数据类型都可以不一样
// 但是和字符串不能修改不同, 这个可以通过下标访问元素进行修改
```

### 嵌套数组(Nasted Array) / 多维数组(Multi dimensional array)  

```javascript
var ourArray = [["The universe", 42], ["Everything", 101010]];
// 用 bracked notation 访问多维数组
var myData = ourArray[0][1];
// 这个第一个下标访问的是最外层的数组, 第二个下标访问的是第二个数组
```

### 使用`push()`和`pop()`方法 来操作数组 (manipulate arrays with `push()` & `pop()` )

`push()`方法可以在数组的末尾对元素进行追加

```javascript
var ourArray = ["Stimpson", "J", "cat"];
outArray.push(["Happy", "Joy"]);
// 现在数组 等效 ["stimpson", "J", "cat", ["Happy", "Joy"]]
outArray.push("bit","magic");
// 现在数组 等效 ["stimpson", "J", "cat", ["Happy", "Joy"], "bit", "magic"];
console.log(ourArray[ourArray.length - 1]); // 查看最后一个元素是什么
```

`pop()`方法可以从数组末尾删除元素, 并且将被删除的值返回

```javascript
var ourArray = [1,2,3];
var pickUp = ourArray,pop();
// 现在 pickUp = 3, ourArray = [1,2];
```

### 使用`shift()`和`unshift()` 来操作数组 (manipulate arrays with `shift()`&`unsift()`)

前面前面的`push()`可以对元素进行追加. 但是要在数组首位进行添加元素的话,可以使用 `unshift()`；相对于在数组末尾进行删除的`pop()`, 要在数组首位进行删除的话, 可以使用`shift()`

## 函数 (Write Reusable Code with Functins)

```javascript
function learnFunc(arg) { // 参数甚至连类型都不用写, 不愧是"弱类型"语言
    console.log("Helloworld");
}
```

跟别的语言很像, 只不过 前面加一个 function.

### 参数默认值

```javascript
// 直接在参数表中进行赋值, 这个值将作为这个参数的默认值. 并且可以省略
function helloworld(hey, money = 0) { 
} // 其中的 money 默认值是 0;
```

### 剩余操作符(Rest Operator)

使用剩余操作符可以让参数的个数发生变化.

```javascript
// 累加所有元素
const sum = (function() {
    return function sum(...args) {
        return args.reduce((a, b) => a + b, 0);
    };
})();
console.log(sum(1, 2, 3, 4)); 
// 这里 reduce() 会在后面讲到
// 使用剩余操作符, 使得参数个数可变, 之后在使用redece()这种有回调的函数, 可以十分灵活的求和.
```



### 全局变量, 局部变量和方法 (Global Scope & Local Scope and Function)

`Scope`指的就是visibility of variables.也就是作用域. JS中的作用域分为全局和局部.

只要在函数的大括号外面定义的都是全局变量. 而在函数体内的变量都是局部变量

值得注意的是, 如果没有注明 `var` , 即使变量在函数里面, 那么变量将自动被声明成全局变量.

当局部变量和全局变量重名时, 局部变量会覆盖全局变量.

```javascript
var myGlobal = 10;
function fun1() {
    // Assign 5 to oopsGlobal Here
    oopsGlobal = 5; // 这里没有写 var, 会被自动声明为全局变量
}
function fun2() {
    var output = "";
    if (typeof myGlobal != "undefined") {
        output += "muGlobal: " + myGlobal;
    }
    if (typeof oopsGlobal != "undefined") {
        output += "oopsGlobal: " + oopsGlobal;
    }
    console.log(output);
}
fun1();
fun2();
```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/img/after-using-global-varieety-and-funcs.png)

### 理解函数中返回的为定义值 (Understanding Undefined Value Returned from a Function)

如果一个函数没有返回值, 那么它将返回`undefined`

## 控制语句

### if 语句 (Conditiona  Logic with if Statements)

```javascript
if (typeof myGlobal != "undefined") {
    output += "myGlobal:" + myFGlobal
 } else if (1 + 1 === 1) {
     output = "hello"
 } else {
     output = "shit"
 }
if (typeof oppsGlobal != "undefined") {
    
}
```

### switch 语句

```javascript
switch(yal) {
    case 1:
        answer = "alpha";
        break;
    case 2:
        answer = "beta";
    	break;
    default:
        answer = "gamma";
        break;
}
```



### for 循环 ( for loop )

```javascript
var ourArray[];
for (var i = 0; i < 5; i++){
    ourArray.push(i);
}
```

### while 循环 (iterate with while loops)

```javascript
var myArray = [];
var i = 0;
while(i < 5) {
    myArray.push(i);
    i++;
}
var j = 0;
while()
```

### Do...While Loops

```javascript
do {
    console.log("Helloworld");
}while(i === 0);
// 先执行, 再查找条件
```



## JS 对象(Object)

### 定义

```javascript
// 定义对象, 用来存储键值对
var ourDog = {
    "name": "Camper",
    "legs": 4,
    "tai ls": 1,
    "friends": ["everything!"]
    // property(属性) : data(数据)
};

```

### 使用点记录法访问对象 (Dot Notation )

```javascript
var ourDogName = ourDog.name; // 同时可以进行修改
```

### 使用括号记法访问对象 (Bracket Notation)

有的时候, 我们的对象属性(property)会含有空格, 这时候就不能使用点记法进行表示了, 需要使用括号记法, 当然,其他的时间也可以使用括号记法来访问对象

```javascript
var ourDogName = ourDog["name"]; // 同时也可以进行修改
var ourDogTail = ourTail['tai ls']; // 单引号当然也可以
```

### 向对象中添加和删除属性 (Add&Delete proprieties)

```javascript
var ourDog = {
    "name": "Camper",
    "legs": 4,
    "tai ls": 1,
    "friends": ["everything!"]
    // property(属性) : data(数据)
};
ourDog.bark = "bow-wow";
ourDog['bark'] = "woof";
// 这样就向对象中添加了属性
delete ourDog.bark; // 这样就从对象中删除了属性

```

### 在对象的键值中进行查找

可以十分方便的在对象里进行查找

```javascript
function phoneticLookup(val) {
    var lookup {
		"alpha": "Adams",
        "Bravo": "Boston".
        "foxtrot": "frank"
};
result = lookup[val];
return result;
}
// 也可以检查是否有这个属性, 使用 hasOwnProperty()
var myObj = {
  gift: "pony",
    pet: "kitten",
    bed: "sleigh"
};
function checkObj(checkProp) {
    if(myObj.hasOwnProperty(checkProp)) {
        return myObj[checkProp];
    } else
        return "not found"
}
```

### 操作复杂对象(Manipulating Complex Objects)

```javascript
var myMusic = [
    {
        "artist": "Billy Joel",
        "title": "Piano Man",
        "release_year": 1973,
        "formats": [
            "CD",
            "8T",
            "LP"
        ],
        "gols": true
    }
];
// 对象可以和数组可以了灵活组合, 像这样数组[{对象[数组]},]也没问题
console.log(JSON.parse(JSON.stringify(myMusic)));
// JSON.parse() takes a JSON string and transforms it into a JavaScript object. JSON.stringify() takes a JavaScript object and transforms it into a JSON string.
```

## 类(class)

这个比前面的对象更接近与 java 中的类和对象

```javascript
class SpaceShuttle {
    constructor(targetPlanet) { // 这里写构造函数
        this.targetPlanet = targetPlanet;
    }
}
var zeus = new SpaceShuttle('Jupiter');
console.log(zeus.targetPlanet);
// 也可以不用 class 关键字来创建类
var SpaceShuttle = function(targetPlanet){
    this.targetPlanet = targetPlanet;
}
var zeus = new SpaceShuttle('Jupiter');
console.log(zeus.targetPlanet);
```

## Getter 和 Setter (冷门, 跳过3:15:27)

## import 和 require 的区别

- 使用独立js文件?
- 选择器的使用?

## 常用方法

### 生成随机数 (Generate Randon Numbers)

```javascript
Math.random(); // 生成大于等于0小于1的随机数
Math.floor(); // 返回小于 x 的最大整数
Math.random() * 10； // 生成0到10的随机数
Math.floor(Math.random * 10); // 生成0到10的整数
```

### 转换成Int & 转换为10进制整形(convert into Int & convert 10 based radix)

```javascript
// 使用 parseInt() 将字符串转换成 整形
parseInt(str); // 将str转换成十进制整形

// parseInt(number, radix) 也可以进行类型转换, 将其他进制转换成10进制
parseInt("10101", 2); // 将10101转换成十进制数

```

### 三元运算(Ternary Operator)

```javascript
// 类似于 if - else
if(a == b) {
    return true;
} else {
    return false;
}
// 等效于
return a === b ? true : false;
// 甚至可以组合
return num > 0 ? "positive" : num < 0 ? : "negative" : "zero";

```

![](https://raw.githubusercontent.com/heidaren0000/blogGallery/master/imgs/3-way-operator.png)

### 严格模式 (strict mode)

在 JS 中可以使用严格模式, 来提早知道代码中的错误, 减少怪异行为, 提高运行效率

```javascript
"use strict"; // 这条语句开启严格模式
// 如果将这行语句放在脚本的第一行, 那么整个脚本都会按照严格模式来执行, 
// 如果没有放在脚本的地一样, 那么脚本将以"正常模式"进行
// 将这个语句放在函数第一行, 那么函数将以"严格模式进行"
```

https://www.cnblogs.com/liaojie970/p/7154144.html

### 使用匿名函数(Anonymous Function)和使用箭头(Arrow) 写简化的匿名函数

匿名函数可以使用赋值法和自执行的方式来声明

```javascript
var magic = function(){ // 使用赋值方法声明匿名函数
    return new Date();
};
var magic = () => {	// 简化写法, 和上面等效
    return new Date();
};
// 如果你只有一个返回语句的话, 甚至可以把大括号和return关键字都省掉
var magic = () => new Date();
// 有参数的话,参数正常写就可以
```

自执行方法:

```javascript
// !开头, 结构相对清晰.
!function() {
    document.write("Helloworld");
}
// youtube课程里经常使用, 刚开始容易乱
(function(){
	document.write("hello");
})();
// 官方推荐使用
(function(){
    document.write("hello");
}());
//
[function(){
    document.write("hello");
}()]
```



### 使用 filter() 和 map() 操作数组

`filter()`可以舍弃不符合条件的数组元素, `map()`可以对数组内元素进行批量操作

```javascript
// 找到数组中所有的正整数并平方
const squadIntegers = arr.filter(num => Number.isInteger(num) && num > 0).map(x => x * x); // 第一个num作为匿名函数的参数斌没有括号, 是因为只有一个参数的时候可以省略括号. filter() 和 map() 的参数都是当前数组元素
```

### 使用 reduce() 方法操作数组

```javascript
arr.reduce(callback, [initialValue])
// callback 是回调函数, 包含四个参数
// - perviousValue(上一次调用的返回值, 第一次调用是初始值)
// - currentValue(当前被操作的值)
// - index(当前元素在数组中的索引)
// - array(调用的数组)
// 后两个省略的话, 就是从被操作数组第一个元素开始, 直到最后一个元素,  初始值省略的话会从 0 开始.
```

http://www.fly63.com/article/detial/4612

```javascript
var numbers = [65, 44, 12, 4];
function getSum(total, num) {
    return total + num;
}
function myFunction(item) {
    document.getElementById("demo").inneHTML = number.reduce(getSum, 0);
}
// 常规写法
const sum = (function() {
    return function sum(...args) {
        return args.reduce((a, b) => a + b, 0);
    };
})();
console.log(sum(1,2,3,4));
// 使用抽象函数和剩余操作符
```

### 传播符号来复制数组(Spread Operator to Copy Array)

数组的名称和数组本身是引用关系, 也就是说如果我们使用数组的时候, 容易出错

```javascript
const arr1 = ['JAN', 'FEB', 'MAR', 'APR', 'MAY'];
let arr2;
(function() {
    arr2 = arr1; // 
    arr1[0] = 'potato';
})();
console.log(arr2);
// 这时, arr2中的 [0] 也变成 'potato' 了
// 要想真正的进行数组的赋值, 就需要使用 Spread Operator 来继续了
const arr1 = ['JAN', 'FEB', 'MAR', 'APR', 'MAY'];
let arr2;
(function() {
    arr2 = [...arr1]; // 使用 Spread Operator 将 arr1 复制给 arr2
    arr1[0] = 'potato';
})();
console.log(arr2);
// 输出 JAN
```

### 使用解构赋值来分解对象为变量(Use Destructuring Assignment to Assign Variables from Objects)

```javascript
var voxel = {
    x: 3.6,
    y: 7.4.
    z: 6.54
}
const {x : a, y : b, z : c} = voxel;
// 将 voxel 中的 x 给 a, b 给 y, z 给 c.
// 对于对于嵌套对象, 如下处理
const LOCAL_FORECAST = {
    today: { min: 72, max: 83 },
    tomorrow: { min: 73.3, max: 84.6 }
};
const { tomorrow : { max : maxOfTomorrow }} = forecast;
const { today : { max : maxOfToday, min : minOfToday }} = forecast;
```

### 解构赋值分解数组(Destructuring Assignment to Assign Variables From Arrays)

```javascript
const [z, x] = [1, 2, 3, 4, 5];
// 这样就会将前两个元素给 z 和 x, 按照顺序进行
// 如果要跳过某些元素, 直接在其位置留空即可
const [z, x, , c] = [1, 2, 3, 4, 5];
// 这样 c 得到的就是 4
// 比较疯狂的用法是拿来进行交换值
let a = 8, b = 6;
[a, b] = [b, a];
// 这样可行, 没开玩笑, a 和 b 交换了
```

### 结构赋值作为参数(太无聊了直接跳过3:05:06)

### 使用模板标识符创建字符串 (Create Strings  using Template Literals)

借助这个可以非常方便的在字符串中使用变量, 用反引号` `` ` 来替换双引号和单引号, 同时可以包含特定语法`${expression}`的占位符. 占位符的表达式和周围的文本回忆起传递给一个默认函数, 将所有的部分连接起来. 同时模板字符串可以包含多行文本.

如果一个模板字符串由一个表达式开头(通常是函数), 那么会在模板字符串之后调用表达式.

```javascript
var normal = "String text line 1\n\string text line 2";
var template = `string text line 1
string text line 2`;
// 多行特性
var normal = "String is combined with " + (a + b) + " OK";
var template `String is combined with ${a + b} OK`;
function tag(strings, ...values) {
    console.log(strings[0]); // "Hello"
    console.log(strings[1]); // "world"
    console.log(values[0]); // a + b 的值
}
console.log(tag`Hello ${a + b} world`);

//在标签函数的第一个参数中，存在一个特殊的属性raw ，
//可以通过它来访问模板字符串的原始字符串。

            function tag(strings, ...values) {
                console.log(strings.raw[0]);
                // "string text line 1 \n string text line 2"
            }
            tag `string text line 1 \n string text line 2`;
```

https://www.cnblogs.com/qingmingsang/articles/5513064.html