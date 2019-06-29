#  NodeJS day01

---
本阶段通过NodeJS来了解后端，以及更深入的了解javacscript这门语言



## 学习目标
1. 【基础概念】
   + 什么是node.js以及node.js的特点
   + node.js适合做什么
   + nodeJS中相关的概念：什么是模块化、什么是Common.js模块化规范
   + 模块、包、npm、包加载机制
   + etc...
2. 【基本能力】
   + 掌握node.js中基本API的使用
   + 能够使用npm管理包
   + 能够使用ES6新语法
   + 能够使用node.js创建基本的web服务器
3. 【基本实战】
   + 能够使用Express框架、结合mysql数据库编写网站服务
## 1.0 基本概念
### 1.1 一些杂谈

#### 语言和环境

 - 语言
    - 编写代码的语言规范；程序员遵循特定的语法规范，编写出来的代码，字符和文本代码本身并不具备执行的能力
 - 环境（平台）
    - 提供了执行代码的能力，如果开发者想要看到自己表达的内容体现，就必须依赖特定的执行环境

> 比如，前端所有的效果都必须在浏览器运行，后端的代码必须在服务器中运行



#### 前端和后端

| 开发者  | 能力                                 |
| ---- | ---------------------------------- |
| 前端   | 页面结构、美化页面样式、书写页面的业务逻辑、使用Ajax调用后台接口 |
| 后端   | 操作数据库、对外暴露操作数据库的API接口              |
![此处输入图片的描述][1]
![此处输入图片的描述][2]


#### 浏览器大战
##### 1 诞生 1994

上世纪90年代左右，网景公司创造了liveScript，目的为了实现用户交互

##### 2 浏览器大战（一） 1995 - 1997

网景公司Netscape携手sun公司推出的javascript大热
微软的IE浏览器推出了JScript
同时，IE浏览器通过免费在微软中捆绑销售，一举击垮了网景公司

__欧洲计算机制造商协会（ECMA）于1998年6月，制定了ECMAScript 2.0__

##### 3 浏览器大战（二）2004 - 2009

javascript发展毫无起色，网页铺天盖地的“牛皮藓”

雄心不倒的网景公司，浴火重生创造了火狐浏览器（Firefox）

mozilla公司公开了源码，IE发布了第7个版本，此时也冒出了很多其他浏览器

2008年12月，Google Chrome诞生

__由谷歌发布的世界上最快的JS解析引擎（V8）诞生__

![此处输入图片的描述][3]

##### 4 ECMAScript 5.0  2009.12

__总结：浏览器中JS组成：ECMAScript核心 + DOM + BOM__



### 1.2 Node的规范

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。 
Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，使其轻量又高效。

#### 事件驱动

```javascript
// DOM中的事件
$("#btn").on("click", function() {
  	// code
})

// Node中的事件
server.on("request", function() {
  	// code
})
```



#### 非阻塞式 I/O

```javascript
// js从上到下执行，如果前面的未执行，不会先执行下面的内容
// 如果当前执行报错，项目停止执行


// 事件注册、定时器、ajax的回调函数，都是在事件队列中排队执行
// 提高代码执行的效率
// 举例：食堂排队
```

（事件循环的概念将在明天详细讲）

#### npm

Nodejs的包管理器，是全球最大的开源库生态系统

#### NodeJS组成部分

ECMAScript核心 + __全局成员__ + __核心API模块__

> 注意在Node.js中，没有BOM和DOM，node是提供服务和数据交互的，不是制作web页面的

#### 浏览器中JS和Node中JS的关系

![](D:\课程\课程资料\10_NodeJS\10_NodeJS(笔记)\笔记\images\nodejs-web.jpg)

### 1.3 总结

nodejs是什么？

nodejs可以干什么

 - 像php一样，使用javascript编写符合规范的后端API接口或者网站

 - 使用NodeJS开发一些实用的工具或者包

 - **现代的前端开发中几乎所有的项目都需要编译。编译的平台99%是在node中进行**

 - 基于Socket技术，开发类似于聊天室之类的即时通讯项目

 - 基于Electron环境（https://electronjs.org），开发桌面软件（例如vscode）

 - ect... 

 - [javascript占比]: https://madnight.github.io/githut/#/pushes/2019/1


  ![](D:\课程\课程资料\10_NodeJS\10_NodeJS(笔记)\笔记\images\image_1b32gl0abfsp1ekp18a117dckf516.png)



---



## 2.0 环境安装

LTS：【推荐在企业中使用】是长期稳定版的安装包，运行稳定、安全

Current：【推荐学习或者尝鲜去使用】，最新的特征，有Node最新特性



### 2.1 安装Node环境

 	双击安装对应版本，最好直接装在C盘

装好了之后，通过cmd打开命令行窗口，执行版本号的查询

![](D:\课程\课程资料\10_NodeJS\10_NodeJS(笔记)\笔记\images\cmd.jpg)


### 2.2 环境变量

如果不识别node命令，可以去查看环境变量中是否存在node路径

一般操作都是系统的环境变量，而不是操作用户的环境变量

![](D:\课程\课程资料\10_NodeJS\10_NodeJS(笔记)\笔记\images\path.jpg)

### 2.3 执行js的方式

| cmd小技巧     | 功能            |
| ---------- | ------------- |
| 使用上箭头      | 快速定位到上一次执行的命令 |
| 使用tab键     | 自动补全路径        |
| 输入cls      | 快速清屏          |
| 按两次 Ctrl+C | 退出REPL环境      |
| 输入ls      | 查看当前目录文件          |
| 输入cd      | 进入指定目录          |



> ##### 在REPL环境中可以执行一些js的语句





---



## 3.0 ES6语法

2015年6月17日 ECMAScript 6发布正式版本，也叫做ECMAScript 2015，此后每年更新一次

ES6的语法在大部分的浏览器中都是可以使用，不过还有部分内容浏览器识别不了，如果想使用必须要通过工具提前转译成ES5

### 3.1 变量声明的方式

在以往定义变量时，通常使用var关键声明，它存在很多的缺点

1，存在变量声明提升，降低js代码的可阅读性

2， 没有块级作用域，容易造成变量污染



#### 使用let声明变量

- 不存在变量声明提升，必须要声明之后才能使用，否则报错

- 有块级作用域，{ }就是代码块
- 不能重复声明

```javascript
// 使用let关键字定义变量
console.log(c) // error: c is not defined
let c = 11
c += 1
```

```javascript
for(let i = 0; i < 10; i++) {
  // code
}
console.log(i) // error: i is not defined
```



#### 使用const声明常量

- 同let

- const定义的常量，不能被重新赋值

- 当定义常量的时候，必须同时赋值，不然报错


```javascript
// 使用const关键字定义变量
console.log(d) // error: d is not defined
const d = 11
```

```javascript
const d = 10
// 注意：const定义的常量无法被重新赋值
d = 20 // typeError: Assignment to constant variable.
```

```javascript
// 注意：const定义的常量,必须在定义的时候给一个初始化的值
const d
// SyntaxError: Missing initializer in const declaration
```

```javascript
for(let i = 0; i < 10; i++) {
  const a = "hello"
  console.log(a) // 输出10次 hello
}
// { }有作用域，不同的{ }有不同的作用域
// 举例：点击绑定

// ---------------------------------

const a = "hello"
const a = "hello"
// SyntaxError: Identifier 'a' has already been declared
```



#### 解构赋值

```javascript
let user = {
  	name: "zs",
  	age: 20,
  	gender: "男"
}

/*let name = user.name
let age = user.age
console.log(name)
console.log(age)*/

// 这是解构赋值语法 (冒号代表重命名)
let { name: username, age } = user
username = "ls"
console.log(username) // ls
console.log(age)  // 20

// 如果let改为const，那么在重新赋值的时候会报错
```



### 3.2 箭头函数

- 箭头函数没有自己的this，this指向**定义**箭头函数时所处的**外部执行环境**的this
- 即时调用call/apply/bind也无法改变箭头函数的this
- 箭头函数本身没有名字
- 箭头函数不能new，**会报错**
- 箭头函数没有arguments，在箭头函数内访问这个变量访问的是**外部执行环境**的arguments
- 箭头函数没有prototype

```javascript
// 普通函数
function show(arg1, arg2) {
  	// code
}

// 箭头函数 - 本质上是匿名函数的简写
/* (形参列表) => {
  // code
} */
```



#### this指向

```javascript
btn.onclick = function() {
  	setTimeout(function() {
      	console.log(this) // Window
        this.style.backgroundColor = "red" // 报错
  	}, 1000)
}
```

使用箭头函数后

```javascript
btn.onclick = function() {
  	setTimeout(() => {
      	console.log(this) // <button id="btn">点击<button>
        this.style.backgroundColor = "red" 
  	}, 1000)
}
```



> ### 箭头函数内部的this永远指向箭头函数外面的this



#### 箭头函数-变体

```javascript
/*function add(x, y) {
  	return x + y
}

// 匿名函数
(x, y) => {
  	return x + y
}*/

var add = (x, y) => {
  	return x + y
}
console.log(add(1, 2)) // 3
```

变体1

```javascript
// 如果左侧的形参只有一个，则括号可以省略
var add = x => {
  	return x + 10
}
console.log(add(1)) // 11
```

变体2

```javascript
// 如果右侧只有一行代码，则可以省略花括号和return
var add = (x, y) => x + y
console.log(add(1, 2)) // 3
```

变体3

```javascript
// 如果以上两种情况都出现，则都可以省略
var add = x => x + 20
console.log(add(3)) // 23
```



### 3.3 对象中定义属性和方法

使用快捷方式定义属性和方法

```javascript
let name = "zs"
let age = 22
function show() {
  	console.log('我是zs')
}

/* let person = {
  	name: name,
  	age: age,
  	show: show
}
console.log(person) // {name: 'zs', age: 22, show: [Function: show]} */

let person = {
  name,
  age,
  show,
  say() {
    	console.log("ok")
  }
}
console.log(person)
// { name: 'zs', age: 22, show: [Function: show], say: [Function: say]} */



```


[1]: http://static.zybuluo.com/qiankunfaith/h9azwwsm2zbahr6c7sqok7k0/image_1b2sl8d6n1ulk152t1qlp1ebq40p.png
[2]: http://www.aseoe.com/uploadfile/2016/0831/20160831103230828.jpeg
[3]: http://static.zybuluo.com/qiankunfaith/nyh6e6qivt0e03itdh2544ko/image_1b32geknc1cg517d9lpjfgn16v39.png