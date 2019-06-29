# NodeJS简要扎记

---

​	使用node提供的模块能够解决一些对应的问题

## 1.0 fs文件操作

![node-fs](images\node-fs.jpg)

### 1.1 读取文件内容

![readFile](images\readFile.jpg)

```javascript
// 需求： 使用 node 中提供的 文件操作API， 读取 files 目录下的 1.txt 文档中文本内容
// Node 的三个组成部分： ECMAScript核心 + 全局成员 + 核心API成员
// 核心API成员，在安装Node应用程序的时候，就已经安装到了自己的电脑中
// 如果要访问核心程序员，直接使用 require("核心成员的名称") 就能够导入并使用这些核心成员

const fs = require("fs")

// fs 核心模块中，提供了 fs.readFile 方法， 来读取指定目录下的文件
// fs.readFile 有三个参数
// 参数1： 表示要读取的文件路径
// 参数2： 表示要以什么样的编码格式，来读取指定的文件，默认编码格式为null
// 参数3：当文件读取完成，调用这个callback回调函数来处理读取的结果
//       其中，第一个参数是error对象，第二个参数才是读取的结果
fs.readFile('./files/1.txt', function(err, data) {
  	console.log(err) // null
    console.log(data) // <Buffer 31 31 31>  未 二进制被转换为十六进制
})


```

```javascript
// 加入第二个参数
fs.readFile('./files/1.txt', 'utf-8', function(err, data) {
  	console.log(err) // null
    console.log(data) // 111
})
```



#### 如果读取一个不存在的文件

```javascript
fs.readFile('./files/abc.txt', 'utf-8', function(err, data) {
  	// 如果内容只有一句，可以省略花括号，写在一行上
  	if(err) {
     	return	console.log('读取文件失败:' + err.message)
  	}
  	console.log('读取文件成功， 内容是:' + data)
})

// 读取文件失败：ENOENT：no such file or directory, open'...'
```



### 1.2 指定路径的文件写入内容

![wirteFile](images\wirteFile.jpg)

```javascript
// 需求：调用 fs.writeFile 方法，向 files 目录中，写入一个 2.txt 文档 222

// 1. 导入 fs 文件操作模块
const fs = require('fs')

// 2. 调用 fs.writeFIle 写入文件
// 参数1：路径字符串，表示要把文件内容，写入到哪个文件中
// 参数2：要写入的数据，可以给定一个 字符串
// 参数3：可选参数，表示要以什么格式写入文件内容， 默认以 utf8 格式写入文件 【一般默认不传递第三个参数】
// 参数4：文件写入完成之后的 callback 回调函数
//       在回调函数中，只有一个形参，err错误对象
fs.writeFile('./files/2.txt', '222', function(err) {
  	if(err) return console.log('写入文件失败！' + err.message)
    
    console.log('文件写入成功！')
})
```

改造成箭头函数

```javascript
fs.writeFile('./files/2.txt', '222', (err) => {
  	if(err) return console.log('写入文件失败！' + err.message)
    
    console.log('文件写入成功！')
})
```



### 1.3 追加文件内容

![appendFile](images\appendFile.jpg)



```javascript
// 1. 导入 fs 文件操作模块
const fs = require('fs')

// 2. 调用 fs.appendFile 追加文件内容
// 参数1： 表示要向哪个文件中追加内容，指定一个文件的路径
// 参数2： 表示要追加的具体的内容，可以传递的字符串内容
// 参数3： 可选参数，表示 追加文本内容时候的编码格式 ，如果省略此参数，默认以 utf8
// 参数4： 表示 追加完成之后的回调函数
//        形参err表示追加失败之后的错误结果，如果err为null，表示追加成功
fs.appendFile('./files/2.txt', '\n333', (err) => {
  	if(err) return console.log('追加文件失败！' + err.message)
    
    console.log('追加文件成功！')
})
```



#### 如果追加的文件路径不存在

```javascript
fs.appendFile('./files/ssss.txt', '\n333', (err) => {
  	if(err) return console.log('追加文件失败！' + err.message)  
    console.log('追加文件成功！')
})

// 如果要追加的文件路径不存在，则会先尝试创建这个文件，然后再向文件中追加内容
```



#### 关于 __dirname 的使用

​	使用fs模块操作文件的时候，如果提供的操作路径是 相对路径，

​	则会根据当前执行node命令时的磁盘目录，去拼接提供的文件的相对路径，从而容易出现问题，例如

```javascript
const fs = require('fs')

// 调用 fs.readFile 方法时，提供的第一个参数是相对路径，容易出现问题
fs.readFile('./files/1.txt', 'utf-8', (err, data) => {
  	if(err) return console.log('读取文件失败！' + err.message)  
    console.log('读取文件成功！')
})
```



​	推荐使用node中提供的  __dirname 来解决fs模块操作文件的时候的路径问题

![node-wrong](images\node-wrong.jpg)

```javascript
/*
	注意：在node中， __dirname 表示当前这个文件，所处的目录
*/
```

![node-yes](images\node-yes.jpg)



​	比较完整的一次调用

```javascript
fs.readFile(__dirname + '/files/1.txt', 'utf-8', (err, data) => {
  	if(err) return console.log(err.message)
    console.log(data)
})
```



#### ______dirname 和 __filename 的区别

![dirname_filename](images\dirname_filename.jpg)

```javascript
// __dirname 表示 当前这个文件执行的时候，所处的根目录  只是代表一层目录而已；
console.log(__dirname)
// __filename 表示 当前这个文件的完整路径，路径中包含了具体 的文件名
console.log(__filename)
```



### 1.4 读取指定路径的文件信息



![fs-status](images\fs-status.jpg)



```javascript
const fs = require("fs")

fs.stat(__dirname + '/files/1.txt', (err, stats) => {
  	if(err) return console.log(err.message)
    console.log(stats.size) // 3 (3个字节)
    // 获取文件创建时间
    console.log(stats.birthtime) // 2018年6月7日  18:19:11   2018-06-07T10:19:11.186Z
    // 判断当前是不是一个文件
    console.log(stats.isFile) // true 
    // 判断是不是一个目录
    console.log(stats.isDirectory) // false   
})
```



### 1.5 复制文件

![node-copyfile](images\node-copyfile.jpg)



```javascript
const fs = require("fs")

fs.copyFile(__dirname + '/files/1.txt', __dirname + '/files/1-copy.txt', (err) => {
  	if(err) return console.log('拷贝失败：' + err.message)
    console.log('拷贝成功！')
})
```



### 1.6 案例

​	需求：整理    成绩.txt  文件中的数据到成绩 ok.txt 中，整理后的格式如下

```
小红： 99
小白： 100
小黄： 70
小黑： 66
小绿： 88
```



```javascript
// 1.0 导入fs文件操作模块
const fs = require('fs')

// 2.0 分析需求
// 2.1 使用 fs.readFile 读取成绩文本
fs.readFile(__dirname + '/成绩.txt'， 'utf-8', (err, dataStr) => {
  	if(err) return console.log('读取文件失败：' + err.message)
    //console.log(dataStr)
    // 2.2 把读取到的文本，处理成标准的格式
    // 使用字符串的 split 方法分割字符串
    let arr = dataStr.split(' ')   //   会多出来很多空白的一项
    
    // 循环arr数组，判断 当前循环的这一项是否大于0   如果大于0 才有操作的必要
    let newArr = []
    arr.forEach(item => {
      	if(item.length > 0) {
          	// 把＝替换成：
          	let newScore = item.replace('=', ": ")
            // console.log(newScore)
            newArr.push(newScore)
      	}
    })
    //console.log(newArr)
    
    // 2.3 把数据的结果，输入到新的文件中
    fs.writeFile(__dirname + '/成绩 - ok.txt', newArr.join('\n'), (err) => {
      	if(err) return console.log('写入失败：' + err.message)
        console.log('处理成绩成功')
    })	
    
})
```





---



## 2.0 path路径操作



### 2.1 path.join

![path-join](images\path-join.jpg)

```javascript
// 最佳实践：以后只要涉及到路径拼接，一定要使用 path.join() 方法
fs.readFile(path.join(__dirname, './files/1.txt'), 'utf8', (err, dataStr) => {
  	if (err) return console.log(err.message)
  	console.log(message)
})
```



### 2.2 其他方法

#### path.seq

​	获取分隔符

```javascript
const path = require('path')
console.log(path.sep)  // \   :在windows中都是反斜线
```



#### path.basename(path)

​	path.basename() 方法返回一个 path 的最后一个部分

```javascript
const path = require('path')
path.basename('/foo/bar/baz/asdf/quux.html') // quux.html
```



#### path.dirname(path)

​	获取文件目录

```javascript
const path = require('path')
const str = 'c:/a/b/c/1.txt'
console.log(path.dirname(str)) // c:/a/b/c
```



#### path.extname()

​	获取文件扩展名

```javascript
const path = require('path')
const str = 'c:/a/b/c/1.txt'
console.log(path.extname(str)) // .txt
```

​	

​	总结：path提供了更多路径相关的方法

- 不用担心不同系统斜线的问题
- 拼接相对路径更加方便，可以逐级拼接，可以向上级目录拼接





---



## 3.0 单线程和异步（重点）

> 
>
> #### javascript的解析和执行一直是单线程的，但是宿主环境（浏览器或者node）是多线程的
>
> 

​	异步任务是由宿主环境开启子线程完成，并通过__事件驱动、回调函数、队列__，把完成的任务，交给主线程执行

​	javascript引擎，一直在做一个工作，就是__从任务队列里提取任务，放到主线程里执行__





![](D:\课程\课程资料\10_NodeJS\10_NodeJS(笔记)\笔记\images\JS_eventloop2.png)

 	**JS线程**拿到一份代码，会一行一行的执行，并不关心回调，并且会把回调扔给**其他线程**。当回调的条件满足时，其他线程会把回调推到一个叫 “**异步队列**” 的地方，等待被执行。每次JS线程把 “这一轮（事件循环）” 代码执行完成后，就称为一次事件循环，本次事件循环完成后，会去检查“异步队列”里的回调，如果有回调在等候，拿出其中第一个，继续以上逻辑，进入下一轮事件循环。

- 浏览器的渲染发生在事件循环之间
- 给某个函数a传入回调，并执行a，回调是否异步取决于a本身怎么调用回调

### 3.1 异步执行任务的好处 

​	作用：要把耗时的操作放到异步中执行

​	好处：能够提高耗时任务执行的效率，提高主线程的速度，提高js解析引擎的工作效率



---



## 4.0 模块化

​	模块化是一种开发思想，是社区的开发者为了统一规范拟定的策

### 4.1 什么是模块化

   	这些都是社区制定出来的模块规范，目的都是一样的，都是为了解决javascript没有模块化系统的问题，他们都有如何定义模块成员，以及模块成员之间如何进行通信交互的规则



> #### CMD (Common Module Definition) 模块定义规范，

   	就是API，别人约定好的东西，已经有实现, 国人制作的规范

​	CMD其实就是SeaJS这个模块加载器在推广过程中定义的一个模块规范

> #### AMD(Asynchronous Module Definition)，异步模块加载机制

   	与CMD的API极其相似，几乎一样，老外制作的规范

​	AMD其实就是RequireJS这个模块加载器推广过程中定义的一个模块规范

> #### CommonJS

​            NodeJS中的模块化解决方案，也是一些API

> #### ECMAScript 6

   	这几个模块的开发者互相看不起，所以又出现了‘百家争鸣’

   	2015年9月份，ECMAScript官方推出了ECMAScript 6语言标准，在最新的ES6语言规范标准中，已经制定了JavaScript模块化规范，‘export import’，不过前端喜欢追赶新技术，甚至把没有推出来的标准都直接用上生产环境



### 4.2 什么是CommonJS规范

​	是一套javascript的模块化规范，规定了 模块的特性 和 各模块之间如何相互依赖

​	特点：同步加载模块，不适合在浏览器端使用

​	要了解三个成员，

​		module是个对象表示当前这个模块

​		require是方法用来导入模块

​		export是一个对象用来暴露成员

> #### 在浏览器端，适合使用异步的模块，比如AMD或者CMD

​	

### 4.3 全局作用域和模块作用域

​	全局作用域使用 global 来访问，类似于浏览器中的window

​	每个javascript文件

#### 模块作用域

​	以下表示两个js文件

```javascript
var a = 10
// 注意：每个 js 文件，都是一个独立的作用域
//      外界在 require 这个 js 文件的时候，默认无法访问 js 文件中的任何私有成员
```

​	第二个文件

```javascript
// 注意：这是 程序员使用require 导入自己的js模块
const m1 = require('./01.模块1.js')
console.log(a)
// a is not defined

console.log(m1)
// {}
```



#### 全局作用域

```javascript
// global就是浏览器中的window
console.log(global)
// 会打印很多数据
```

> 细节：在window中，可以省略window，全局的东西默认都是window的
>
> ​	   在node中，每一个直接定义的方法，变量，都是默认属于模块作用域的

```javascript
var b = 20
global.b = b
// 这样就可以输出这个值了
console.log(global.b)
```



### 4.4 global在模块之间共享成员

​	一般在企业中**不推荐使用**，因为会存在全局变量污染问题

```javascript
var a = 10

function say() {
  	console.log("okokok")
}

global.a = 10
global.say = say
```

​	在另外一个文件中

```javascript
const m1 = require('./03.模块1.js')
console.log(global.a)
// 10
global.say()
// okokok
```



### 4.5 模块作用域中共享成员

#### module（模块标识）

​	module属性是CommonJS规范中定义的，它是一个对象，表示一个具体的js模块；

#### require（引用模块）

​	每一个实现了CommonJS规范的模块，必须定义一个require()函数

​	使用这个require函数，就能够很方便的导入其他 模块中的成员，供自己使用

#### exports（暴露模块成员）

​	每一个模块中，如果想要把自己的一些私有成员，暴露给别人使用，那么，必须实现一个exports对象

​	通过exports对象，可以方便的把模块内私有的成员，暴露给外界使用

```javascript
console.log(module)
// exports 默认是属于 module
console.log(module.exports)
// {}

// 默认在一个模块中，向外暴露成员，需要使用 module.exports
// 在模块中，如果要导入其他模块，需要使用require
```

![module](images\module.jpg)



#### 使用模块作用域暴露成员

```javascript
var a = 10
var b = 20
var say = function() {
  console.log('hello')
}

exports.a = a
exports.b = b
exports.say = say
```



```javascript
const m1 = require('./05.m1.js')
// console.log(m1)
// {a: 10}
// console.log(m1)
// {a: 10, b: 20}
console.log(m1)
// {a: 10, b: 20, say: [Function: say]}
```



### 4.6 module.exports 和 exports

​	module.exports 和 exports 默认引用了同一个空对象

```javascript
console.log(module.exports)
// {}
console.log(exports)
// {}
console.log({} === {})
// false
console.log(module.exports === exports)
// true
```

​	

​	module.exports 和 exports 作用一致，都可以向外暴露成员

```javascript
var a = 10
var b = 20

exports.a = a
module.exports.b = b
```

​	在另外一个文件中，找到该对象

```javascript
const m1 = require('./07.test1.js')
console.log(m1)
// {a: 10, b: 20}
```



​	一个模块作用域中，向外暴露私有成员时，永远以 module.exports 为准

```javascript
var a = 10
var b = 20

// 没有以对象的方式赋值, 而是直接改写
exports = a 
module.exports = b
```



```javascript
const m1 = require('./07.test1.js')
console.log(m1)
// {20}
```



> #### ！！！为了防止一些不必要的情况发生，在开发中，推荐使用 module.exports



## 课后作业：

实现一个模块：

1. 暴露一个函数

2. 函数功能：extend(obj1,obj2) 扩展obj1,将obj2上的obj1没有的属性复制一份给obj1

   ```javascript
   
   let obj1 = {a:1}
   extend(obj1,{a:2})   
   console.log(obj1) //{a:1}
   ```

   

   ```javascript
   let obj1 = {a:1}
   extend(obj1,{b:2})   
   console.log(obj1) //{a:1,b:2}
   ```

   

   
