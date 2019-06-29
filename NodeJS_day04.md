> # NodeJS简要札记

---



### 安装nodemon

​	安装nodemon，在执行文件的时候，即使服务器修改了，也不需要自己再手动重启了

​	`npm i nodemon -g`



```javascript
// 1.0 导入http模块
const http = require('http')
// 2.0 创建web服务器server对象
const server = http.createServer()
// 3.0 给server对象监听事件
server.on('request', (req, res) => {
    // 3.1 给客户端返回数据
    res.end('hello world.')
})
// 4.0 服务器挂起
server.listen('3000', () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



## 1.0 Express框架

​	Express框架是一个基于Node.js的框架，对http模块进行了更强大的封装

​	也就是不再使用之前的http模块，而是使用express的方式处理  __请求__

​	`npm i express -S`



### 1.1  使用express快速创建服务器

```javascript
// 1.0 导入模块
const express = require('express')
// 2.0 创建web服务器app对象
const app = express()
// 3.0 app.get方法，监听用户请求，指定用户请求的地址和回调函数
app.get('/', (req, res) => {
    // res.end('hello world.') 不能直接识别中文，需要写响应头
    res.send('你好世界。')
})
// 4.0 启动服务器
app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



### 1.2 快捷方法

#### res.send()

```javascript
const express = require('express')
const app = express()
app.get('/', (req, res) => {
    // 1. 发送字符串
    // res.send('你好世界。')
    // 2. 发送数组
    // res.send(['zs', 'ls', 'ww'])
    // 3. 发送对象
    // res.send({name: 'zs', age: 22})
    // 4. 发送二进制
    res.send(new Buffer(111))
})
app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



#### res.sendFile()

```javascript
const express = require('express')
const path = require('path')

const app = express()
app.get('/', (req, res) => {
    // 1. 要求绝对路径
    // res.sendFile(path.join(__dirname, './views/index.html'))
    // 2. 可以使用相对路径
    res.sendFile('./views/index.html', {root: __dirname})
})
app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```





### 1.3 托管静态资源文件

​	app.use() 是注册中间件

​	express.static() 是中间件的一种

```javascript
const express = require('express')
const app = express()
// 1. express.static() 可以把指定目录托管为静态目录，这样请求的页面可以直接访问
app.use(express.static('./views'))

// 2. 指定虚拟目录   127.0.0.1:3000/public/views/index.html
// app.use('public', express.static('./views'))
app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



> #####express中间件 express.static('./目录') 专门用来托管处理请求页面
>
> #####app.use() 是使用它



---



## 2.0 使用模板



### 2.1 使用ejs创建模板

​	`npm i ejs -S`



```javascript
const express = require('express')
const app = express()

// 1.0 设置模板引擎的方式
app.set('view engine', 'ejs')
// 2.0 设置veiws指向模板文件的目录
app.set('views', './ejs_pages')

app.get('/', (req, res) => {
    // 3.0 调用response的render，参数1：模板文件路径，参数2：数据对象
    res.render('index.ejs', {
        name: 'ls', 
        age: 108, 
        hobby: ['majoog', 'kongfu', 'food']
    })
})

app.listen(3000, () => {
    console.log('http://127.0.0.1:3000')
})
```

​	index.ejs 模板文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>这是ejs渲染的内容</h1>
    <p>姓名：<%= name %></p>
    <p>年龄：<%= age %></p>
    <p>爱好：
        <% hobby.forEach(item => { %>
            <span><%= item %></span>
        <% }) %>
    </p>
</body>
</html>
```





### 2.2 使用art-template模板

​	`npm i art-template express-art-template -S`



```javascript
const express = require('express')
const app = express()

// 1.0 定义模板引擎的名称
app.engine('html', require('express-art-template'))
// 2.0 定义模板引擎的方式
app.set('view engine', 'html')
// 3.0 定义views指向的渲染目录
app.set('views', './art_pages')

app.get('/', (req, res) => {
    // 4.0 调用render方法进行渲染
    res.render('index.html', {
        name: 'zs',
        age: 38,
        books: ['三国演义', '红楼梦', '西游记', '名侦探柯南']
    })
})

app.listen(3000, () => {
    console.log('http://127.0.0.1:3000')
})
```



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>这里是art-template渲染的模板数据</h1>
    <p>姓名： {{ name }}</p>
    <p>年龄： {{ age }}</p>
    <p>
        四大名著
        {{ each books }}
            <span>{{ $index }} ---  {{ $value }}</span>
        {{ /each }}
    </p>
</body>
</html>
```





---



## 3.0 封装路由模块

​	在开发中，经常会使用分模块开发，在不同的模块处理不同的业务，比如像处理客户端请求这个事情，是很繁琐的，所以不需要在主文件中进行，而转交一个路由模块处理

### 3.1 服务模块

```javascript
const express = require('express')
const app = express()

// 1.0 导入路由模块
const router = require('./07.router.js')
// 2.0 使用路由功能
app.use(router)

app.listen(3000, () => {
    console.log('server running at 127.0.0.1:3000')
})
```



### 3.2 路由模块

```javascript
const express = require('express')
// 1.0 创建路由对象
const router = express.Router()

// 2.0 使用路由对象的方法处理客户端请求
router.get('/', (req, res) => {
	res.sendFile('./views/index.html', { root: __dirname })
})
router.get('/movie', (req, res) => {
	res.sendFile('./views/movie.html', { root: __dirname })
})
router.get('/about', (req, res) => {
	res.sendFile('./views/about.html', { root: __dirname })
})

// 3.0 向外暴露模块
module.exports = router
```



---



## 4.0 中间件



### 4.1 五种中间件

 - 应用级别中间件

```javascript
var app = express()

app.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
})
```

 - 路由级别中间件

```javascript
var app = express()
var router = express.Router()

router.use(function (req, res, next) {
  console.log('Time:', Date.now())
  next()
})
```

 - 错误处理中间件
 - 唯一内置中间件

```javascript
app.use(express.static('./views'))
```

 - __第三方中间件__



### 4.2 中间件案例

```javascript
const express = require('express')
const app = express()
// 导入处理查询字符串的模块
const querystring = require('querystring')

// 1.0 定义中间件
app.use((req, res, next) => {
    let dataStr = ''
    // 1.1 注册data事件，接收数据片段chunk
    req.on('data', chunk => {
        dataStr += chunk
    })
    // 1.2 注册end事件，代表请求结束，打印数据，并且调用next方法执行后续操作
    req.on('end', () => {
        console.log(dataStr)
        // 把 username=ls&password=123456 解析成 {username: 'ls', password: 123456}
        let obj = querystring.parse(dataStr)
        req.body = obj
        next()
    })
})

// 应用级别的中间件
// 2.0 处理开始的请求根目录页面文件
app.get('/', (req, res) => {
    res.sendFile('./09-form.html', {root: __dirname})
})
// 3.0 通过form表单发送一个post请求
app.post('/postdata', (req, res) => {
    console.log(req.body)
    res.send('假如说这是请求之后的页面')
})

app.listen(3000, () => {
    console.log('http://127.0.0.1:3000')
})
```



```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h3>演示中间件</h3>
    <form action="/postdata" method="POST">
        <p>
            用户名： <input type="text" name="username">
        </p>
        <p>
            密码： <input type="password" name="password" id="">
        </p>
        <p>
            <input type="submit" value="提交">
        </p>
    </form>
</body>
</html>
```



---



## 5.0 数据库连接和操作

​	在操作数据库之前，需要启动 PHPstudy， 因为它内部集成了mysql的环境



### 5.1 在数据库中新建数据

​	1， 连接数据库，连接名随便取

​	2，创建数据库，数据库名是连接的时候需要的名称

​	3，创建表，名字慎重，因为操作的就是这个表名



### 5.2 创建数据库连接和查询

​	`npm i mysql -S`



```javascript
const mysql = require('mysql')
const conn = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'hangzhou01'
})

// 3.0 调用查询语句  参数1：sql语句  参数2：回调函数
const sqlStr1 = 'select * from users'
// ！！！注意，这里第二个参数是回调函数
conn.query(sqlStr1, (err, result) => {
    if(err) return console.log('查询失败，原因是：' + err.message)
    console.log(result)
})
```



### 5.3 新增

```javascript
const user =  {name: '小黄', age: 22, gender: '女'}
const sqlStr2 = 'insert into users set ?'
// ！！！ 这里函数接收的第二个参数是 第一条数据的名称
conn.query(sqlStr2, user, (err, result) => {
    if(err) return console.log('新增失败，原因是：' + err.message)
    console.log(result)
})
```



### 5.4 修改

 ```javascript
const user =  {id: 2, name: '小绿', age: 23}
const sqlStr3 = 'update users set ? where id = ?'
// ！！！这里传入的第二个参数是一个数组，并且要和上面的数组一一对应上
conn.query(sqlStr3, [user, user.id], (err, result) => {
    if(err) return console.log('修改失败，原因是：' + err.message)
    console.log(result)
})
 ```



### 5.5 删除

```javascript
const user =  {id: 2, name: '小绿', age: 23}
const sqlStr2 = 'delete from users where id = ?'
// ！！！这里的第二个参数是对应的id
conn.query(sqlStr2, 2, (err, result) => {
    if(err) return console.log('删除失败，原因是：' + err.message)
    console.log(result)
})
```

