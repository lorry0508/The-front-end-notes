> # NodeJS简要札记

---



###  模块成员的分类

> 模块成员，根据一些区别，又可以分为三大类： 核心模块、第三方模块、用户自定义模块

#### 核心模块

用法：`require('核心模块名字')`

#### 第三方模块（包）

用法：`require('第三方模块名字')`

#### 用户自定义模块

用法：`require('路径')`

## 1.0 包

​	

### 1.1 介绍包的定义和结构

​	在模块的基础上进一步深层抽象的文件夹，里面包含了关于使用它的各种文件

​	

#### 结构

​	包都要以一个单独的目录而存在

​	package.json必须在包的顶层目录下

​	package.json必须要符合JSON格式，并且必须包含如下三个属性：

​		name：包的名字

​		version：包的版本号

​		main：表示包的入口文件

​	二进制文件应该在bin目录下，javascript代码应该在bin目录下，文档应该在doc目录下，单元测试应该在test目录下

```javascript
/*
	name: 包的名称，必须是唯一
	description：包的简要说明
	version：符合语义化版本识别规范的版本字符串
	keywords：关键字数据，通常用于搜索
	maintainers： 维护者数组，每个元素都要包含name、email、web可选字段
	contributors：贡献者数组，格式与maintainers相同，包的作者应该是贡献者数据的第一个元素
	bugs：提交bug的地址，可以是网址或者电子邮件地址
	licenses：许可证数组，每个元素要包含type和url字段
	repositories：仓库托管地址数组，每个元素要包含type、url和path字段
	dependencies：包的依赖，一个关联数组，由包名称和版本号组成
	devDependencies：开发者依赖项，表示一个包在开发期间用到的依赖项
*/
```



### 1.2 自定义calc计算包



![readme](images\readme.jpg)

​	制作package.json

```json
{
  "name": "calc",
  "version": "0.0.1",
  "main": "./lib/main.js"
}
```



![package-eg1](images\package-eg1.jpg)



#### 在lib文件中新建模块

操作add.js

```javascript
// 加法模块
function add(x, y) {
  	return x + y
}

module.exports = add
```



操作sub.js

```javascript
// 减法模块
function sub(x, y) {
  	return x - y
}

module.exports = sub
```



操作mut.js

```javascript
// 乘法模块
function mut(x, y) {
  	return x * y
}

module.exports = mut
```



操作devide.js

```javascript
// 除法模块
function devide(x, y) {
  	return x * y
}

module.exports = devide
```



#### 在main.js中暴露成员

```javascript
// 这个main.js是向外暴露成员的统一入口

// 导入四则运算的模块
const add = require('./add.js')
const sub = require('./sub.js')
const mut = require('./mut.js')
const devide = require('./devide.js')

module.exports = {
  add,
  sub,
  mut,
  devide
}
```



### 1.3 使用文件

​	在测试文件中，使用模块

```javascript
// 下面这种方式，是通过路径标识符导入 calc 包的
const calc = require('./calc')

// console.log(calc)
console.log(calc.add(1, 2))
console.log(calc.sub(5, 3))
```



> ### 引入模块不按照路径

​	新建 node_modules 文件夹，把包扔进去，那么引入的时候，就可以不写路径了

```javascript
// 在导入包的时候，require的名字，必须是包文件夹的名字，而且只有把包放在 node_modules目录中才可以直接使用名称
const calc = require('calc')
```



---



## 2.0 npm

 	npm是一个第三方模块的托管网站，指的就是 'https://www.npmjs.com'

​	npm是node的包管理工具，安装node软件的时候，就已经自动安装了这个管理工具



### 2.1 安装和卸载全局包(i5ting_toc)

​	安装到计算机全局环境中的包叫做全局包，在当前电脑的任何目录下，可以直接通过命令行来访问；

​	运行 npm install 包名 -g 即可，其中 -g 表示把包安装到全局目录中

​	全局安装目录： C:\Users\用户目录\AppData\Roaming\npm

​	一般工具性质的包，才适合安装到全局，大部分情况下不需要

​	将md文档编译成html页面

​	在当前文件下，按住shift键，打开名称窗口

```
npm install i5ting_toc -g  //安装

i5ting_toc -f ./node基础_day02.md   //运行

npm uninstall i5ting_toc -g   //卸载
```





​	

![i5ting_toc](images\i5ting_toc.jpg)



### 2.2 安装和卸载本地包(jquery)

​	跟着项目安装的包叫做本地包，本地包都会被安装到 node_modules 目录下

​	如果拿到一个空项目，必须在当前根目录中，先运行 npm init 或者 npm init -y 命令，初始化一个package.json的配置文件，否则包无法安装到本地项目中

​	运行 `npm i 包名--save` 即可安装本地包，下载到 node_modules 目录下

​		如果安装的是npm 5.x的版本，可以不指令 --save命令，如果是npm 3.x版本，则需要

​	package-lock.json 文件中记录了曾经装过的包下载地址，方便下次直接下载包，能够加快装包的速度，提升装包体验

​	卸载： 使用 npm uninstall/remove 包名 -s/-D 即可卸载指定的本地包

```javascript
/*
	--save 的缩写是 -S
	--save-dev 的缩写是 -D
	install 的缩写是 i
	dependencies 节点，表示项目上线部署时候需要的依赖项
	devDependencies节点，表示项目在开发阶段需要的依赖项，但是当项目要部署上线了，这里的包就不需要了
	
	当使用npm i快速装包的时候，npm会检查package.json文件中，所有的依赖项，然后安装到项目中
	--production 表示只安装dependencies节点记录的包，不安装devDependencies节点下的包，一般项目上线才用
*/
```



​	初始化 package.json

```txt
npm init -y

npm install jquery
```



![npm-init](images\npm-init.jpg)

​	创建到对应的目录下

![install-local](images\install-local.jpg)

​	

### 2.4 解决npm下载慢的问题

​	npm默认下载的时候，连接国外服务器，网速不好下载就会变慢，或者下载失败，所以推荐使用 cnpm

​	`npm i cnpm -g`

​	在装包的时候，只需要把 npm 替换成 cnpm 即可，例如

​		使用cnpm 安装jquery： `cnpm i jquery -S`



---



## 3.0 http模块



### 3.1 web服务器的创建

​	在node中，不需要apache一样的web容器

​	B/S：特指基于浏览器和服务器这种交互形式

> ### 使用http核心模块



#### 创建服务器

```javascript
const server = http.createServer() // 创建服务器
```



#### 绑定监听事件

```javascript
// 绑定事件 并 指定处理函数
server.on('request', function(req, res) {
  	// 请求处理的函数
})
```



#### 启动服务器

```javascript
// server.listen(端口，IP地址，启动成功的回调函数)
```



​	完整的代码

```javascript
// 1.0 导入http核心模块
const http = require('http')
// 2.0 创建web服务器对象
const server = http.createServer()
// 3.0 绑定事件，监听请求
server.on('request', function(req, res) {
  	// req表示客户端相关参数
  	// res表示和服务器相关的参数和方法
  	
  	// 3.1 end方法可以返回数据
  	res.end('hello world')
})
// 4.0 启动服务，设置ip地址和端口
server.listen('3000', 'localhost', function() {
  	console.log('server runnig at 127.0.0.1:3000')
})
```



> #### 解决中文乱码

​	通过设置响应报文头的Content-Type，来指定响应内容的编码类型，从而防止乱码

```javascript
// 3.0 绑定事件，监听请求
server.on('request', function(req, res) {
  	res.writeHeader(200, {'Content-Type': 'text/plain; charset=utf-8'})
  	
  	// 3.1 end方法可以返回数据
  	res.end('hello world')
})
```



### 3.2 不同的url地址返回内容

​	根据不同的请求地址，返回给用户不同的内容，这样不会太单一

```javascript
const http = require('http')
const server = http.createServer()
server.on('request', function(req, res) {
  	console.log(req.url) // 打印客户端请求的url地址
    // 首页的话 打印 / 根路径
    res.writeHeader(200, {'Content-Type': 'text/plain; charset=utf-8'})
    const url = req.url
    if(url === '/' || url === '/index.html') {
      	res.end('首页')
    } else if (url === '/movie.html') {
      	res.end('电影')
    } else if (url === '/about.html') {
      	res.end('关于')
    } else {
      	res.end('404')
    }
})
server.listen('3000', function() {
  	console.log('server running at 127.0.0.1:3000')
})
```



#### 返回标签

​	改造响应报文头的文档类型，才能让浏览器识别标签

​	image/jpg       image/png       image/gif

```javascript
const http = require('http')
const server = http.createServer()
server.on('request', function(req, res) {
    res.writeHeader(200, {'Content-Type': 'text/html; charset=utf-8'})
    const url = req.url
    if(url === '/' || url === '/index.html') {
      	res.end('<h3>首页</h3>')
    }
})
server.listen('3000', function() {
  	console.log('server running at 127.0.0.1:3000')
})
```



#### 返回页面

​	在项目中分别创建index.html、movie.html、about.html

```javascript
// 1.0 引入fs模块读取文件
const fs = require('fs')
// 2.0 引入path核心模块
const path = require('path')

const http = require('http')
const server = http.createServer()
server.on('request', function(req, res) {
    const url = req.url
    
    // 使用fs模块读取首页文件，使用path模块拼接路径，把文件使用res.end返回
    if(url === '/' || url === '/index.html') {
      	fs.readFile(path.join(__dirname, './views/index.html'), 'utf8', (err, data) => {
          	if(err) return res.end('页面读取失败')
            res.end(data)
      	})
    } else if (url === '/movie.html') {
      	fs.readFile(path.join(__dirname, './views/movie.html'), 'utf8', (err, data) => {
          	if(err) return res.end('页面读取失败')
            res.end(data)
      	})
    } else if (url === '/about.html') {
      	fs.readFile(path.join(__dirname, './views/about.html'), 'utf8', (err, data) => {
          	if(err) return res.end('页面读取失败')
            res.end(data)
      	})
    } else {
      	res.end('404')
    }
})
server.listen('3000', () => {
  	console.log('server running at 127.0.0.1:3000')
})
```



> #### 读取文件方法省略 'utf8' ，那么结果就是二进制的内容，但是不会有问题
>
> ​	res接收两种数据类型，String和二进制



#### 加载CSS和JS

​	在请求里面多加两行请求

```javascript
//...
	else if (url === '/css/1.css') {
      	fs.readFile(path.join(__dirname, '/css/1.css'), (err, data) => {
          	if(err) return res.end('404. Not found')
            res.end(data)
      	})
    } else if (url === '/js/1.js') {
      	fs.readFile(path.join(__dirname, './js/1.js'), (err, data) => {
          	if(err) return res.end('404. Not found')
            res.end(data)
      	})
    }
//...
```



#### 优化web服务器

```javascript
// 1.0 引入fs模块读取文件
const fs = require('fs')
// 2.0 引入path核心模块
const path = require('path')

const http = require('http')
const server = http.createServer()
server.on('request', function(req, res) {
    let url = req.url
    if(url === '/') url = './views/index.html'
    fs.readFile(path.join(__dirname, url), (err, buf) => {
    	if(err) return res.end('页面读取失败')
        res.end(buf)
    })
})
server.listen('3000', () => {
  	console.log('server running at 127.0.0.1:3000')
})
```



### 3.3 结合模板引擎实现数据渲染

​	npm init -y

​	npm i art-template



#### 后端内容

```javascript
const path = require('path')
const template = require('art-template')
const http = require('http')
const server = http.createServer()

server.on('request', (req, res) => {
  	const url = req.url
    if(url === '/') {
      	// 使用模板渲染页面
      	// 参数1：要渲染的HTML页面的路径，
      	// 参数2：要渲染的数据，没有数据的话，给一个 {} 对象
      	// 返回值：渲染好的HTML内容，直接通过res.send发送给客户端
      	const htmlStr = template(path.join(__dirname, '/views/1.html'), {
          name: 'zs',
          age: 22,
          hobby: ['吃饭', '唱歌', '跳舞']
        })
        res.end(htmlStr)
    }
})
server.listen('3000', () => {
  	console.log('server is running at 127.0.0.1:3000')
})
```



#### 前端内容

​	模板引擎的语法

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>
	<h1>这是一个动态渲染的页面</h1>
	<h6> 通过 Node 服务器，结合 art-template 模板引擎渲染的动态页面</h6>
	<p>姓名： {{ name }}</p>
	<p>年龄： {{ age }}</p>
	<p>爱好： 
		{{ each hobby }}
			<span>{{ $value }}</span>
		{{ /each }}
	</p>
</body>
</html>
```



### 课后作业：

将今天代码实现一遍：

1. 创建web服务器。
2. 不同URL返回不同内容（包括静态文件）
3. 简单使用art-template模板

### 课后思考：

1. 每次改了代码服务都要重启一遍才能生效
2. 每次手动读取文件，设置响应头比较麻烦，需要已经一种封装好的模块来提供更友好更方便的API
3. 服务端如果要存数据怎么办？需要一种按一定结构，组织长期存放数据的容器。