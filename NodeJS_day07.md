> #  NodeJS简要札记

---



## 1.0 实现登录功能

​	login.ejs 引入jquery，给按钮注册点击事件，监听请求

```html
  <script src="/node_modules/jquery/dist/jquery.min.js"></script>
  <script>
    $(function () {
      $('#form').on('submit', function (e) {
        // 组织表单的默认提交行为
        e.preventDefault()
        $.ajax({
          url: '/login',
          data: $('#form').serialize(),
          type: 'POST',
          dataType: 'json',
          success: function (result) {
            if (result.status !== 200) {
              // 登录失败
              return alert(result.msg)
            }
            location.href = '/'
          }
        })
      })
    })
  </script>
```

​	

​	app.js 后端监听请求

```javascript
// 监听登录请求
app.post('/login', (req, res) => {
  	// 1. 获取到表单中的数据
  	const body = req.body
    // 2. 执行sql语句，查询用户是否存在
    const sql1 = 'SELECT * FROM blog_users WHERE username = ? AND password = ?'
    conn.query(sql1, [body.username, body.password], (err, result) => {
      	if(err) return res.send({msg: '登录操作错误！', satus: 501})
        if(result.length !== 1) return res.send({msg: '登录失败，请重试！', satus: 502})
        // 登录成功
        res.send({msg: 'ok', status: 200})
    })
})
```





---



## 2.0 抽离路由模块



​	在 app.js 中存在了太多的请求监听，这样在将来开发的时候，肯定不适合后期维护和修改

​	新建 router 文件夹，新建 index.js

```javascript
const express = require('express')
const router = express.Router()

// 用户请求的 项目首页
router.get('/', (req, res) => {
    res.render('index.ejs', {})
})

// 将路由对象暴露出去
module.exports = router
```



​	在app.js 中导入路由模块

```javascript
// 导入 router/index.js
const router1 = require('./router/index.js')
app.use(router1)
```



​	在 router 文件夹中，新建 user.js 抽离其他的请求

```javascript
const express = require('express')
const router = express.Router()
// 导入处理时间的模块
const moment = require('moment')

// 导入mysql模块，创建连接
const mysql = require('mysql')
const conn = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'node_item'
})

// 监听客户端注册请求
router.get('/register', (req, res) => {
    res.render('./user/register.ejs', {})
})
// 监听客户端登录请求
router.get('/login', (req, res) => {
    res.render('./user/login.ejs', {})
})
// 监听的是前端的post注册请求
router.post('/register', (req, res) => {
    let body = req.body
    console.log(body)
    // 验证表单数据是否合规则
    if (body.username.trim().length <= 0 || body.password.trim().length <= 0 || body.nickname.trim().length <= 0) {
        return res.send({ msg: '请填写完整的数据', status: 501 })
    }
    // 查询用户名是否重复
    let sql1 = 'select count(*) as count from blog_users where username=?'
    conn.query(sql1, body.username, (err, result) => {
        if (err) return res.send({ 
            msg: '查重操作失败', 
            status: 502 
        })
        if (result[0].count !== 0) return res.send({ 
            msg: 'sorry，用户名重复了', 
            status: 503 
        })
        // 执行注册的业务逻辑
        body.ctime = moment().format('YYYY-MM-DD HH:mm:ss')
        let sql2 = 'insert into blog_users set ?'
        conn.query(sql2, body, (err, result) => {
            if (err) return res.send({ 
                msg: '注册操作失败！', 
                status: 504 
            })
            if (result.affectedRows !== 1) return res.send({ 
                msg: '注册功能失败！', 
                status: 505 
            })
            res.send({ 
                msg: '恭喜亲，注册用户名成功！', 
                status: 200 
            })
        })
    })
})
// 监听登录请求
router.post('/login', (req, res) => {
  	// 1. 获取到表单中的数据
  	const body = req.body
    // 2. 执行sql语句，查询用户是否存在
    const sql1 = 'SELECT * FROM blog_users WHERE username = ? AND password = ?'
    conn.query(sql1, [body.username, body.password], (err, result) => {
      	if(err) return res.send({msg: '登录操作错误！', satus: 501})
        if(result.length !== 1) return res.send({msg: '登录失败，请重试！', satus: 502})
        // 登录成功
        res.send({msg: 'ok', status: 200})
    })
})

module.exports = router
```



​	操作 app.js 导入路由模块

```javascript
// 导入 router/user.js
const router2 = require('./router/user.js')
app.use(router2)
```



### 2.1 通过循环的方式注册路由

​	如果涉及到的路由模块较多，那么在服务模块，可以使用循环的方式注册

```javascript
const fs = require('fs')
const path = require('path')

// 使用循环的方式，进行路由的自动注册
fs.readdir(path.join(__dirname, './router'), (err, filenames) => {
  	if(err) return console.log('读取 router 中的路由失败')
    // console.log(filenames )  // ['index.js'. 'user.js']
    
    //  循环router目录下的每一个文件名
    filenames.forEach(fname => {
      	// 每循环一次，拼接一个完整的路径，使用require导入
      	const router = require(path.join(__dirname, './router', fname))
        app.use(router)
    })
})
```





---



## 3.0 总结MVC三层之间的调用关系



### 3.1 封装 controller 业务处理模块

​	封装的目的是为了保证每个模块的职能单一性，对于路由模块来说，

​	只需要分配 url 地址到 处理函数之间的关系，而不需要关心处理请求的具体实现



​	在 index.js 中把处理函数提取出来

```javascript
const express = require('express')
const router = express.Router()

// 导入自己的业务处理模块，
const ctrl = require('../controller/index.js')
// 用户请求的 项目首页
router.get('/', ctrl.showIndexPage)

// 将路由对象暴露出去
module.exports = router
```



​	新建 controller 文件夹，新建index.js

```javascript
// 展示首页页面
const showIndexPage = (req, res) => {
    res.render('index.ejs', {})
}

module.exports = {
  	showIndexPage
}
```



### 3.2 抽离函数集

​	在 controller 文件中，新建 user.js

```javascript
// 导入处理时间的模块
const moment = require('moment')
const conn = require('../db/index.js')

// 展示注册页面
const showRegisiterPage = (req, res) => {
    res.render('./user/register.ejs', {})
}
// 展示登录页面
const showLoginPage = (req, res) => {
    res.render('./user/login.ejs', {})
}
// 注册新用户请求
const reg = (req, res) => {
    let body = req.body
    console.log(body)
    // 验证表单数据是否合规则
    if (body.username.trim().length <= 0 || body.password.trim().length <= 0 || body.nickname.trim().length <= 0) {
        return res.send({ msg: '请填写完整的数据', status: 501 })
    }
    // 查询用户名是否重复
    let sql1 = 'select count(*) as count from blog_users where username=?'
    conn.query(sql1, body.username, (err, result) => {
        if (err) return res.send({ 
            msg: '查重操作失败', 
            status: 502 
        })
        if (result[0].count !== 0) return res.send({ 
            msg: 'sorry，用户名重复了', 
            status: 503 
        })
        // 执行注册的业务逻辑
        body.ctime = moment().format('YYYY-MM-DD HH:mm:ss')
        let sql2 = 'insert into blog_users set ?'
        conn.query(sql2, body, (err, result) => {
            if (err) return res.send({ 
                msg: '注册操作失败！', 
                status: 504 
            })
            if (result.affectedRows !== 1) return res.send({ 
                msg: '注册功能失败！', 
                status: 505 
            })
            res.send({ 
                msg: '恭喜亲，注册用户名成功！', 
                status: 200 
            })
        })
    })
}
// 登录请求
const login = (req, res) => {
  	// 1. 获取到表单中的数据
  	const body = req.body
    // 2. 执行sql语句，查询用户是否存在
    const sql1 = 'SELECT * FROM blog_users WHERE username = ? AND password = ?'
    conn.query(sql1, [body.username, body.password], (err, result) => {
      	if(err) return res.send({msg: '登录操作错误！', satus: 501})
        if(result.length !== 1) return res.send({msg: '登录失败，请重试！', satus: 502})
        // 登录成功
        res.send({msg: 'ok', status: 200})
    })
}

module.exports = {
  	showRegisiterPage,
  	showLoginPage,
  	reg,
  	login
}
```



​	在 router 文件夹中，操作 user.js，导入操作完的几个请求函数

```javascript
const express = require('express')
const router = express.Router()

// 导入用户相关的函数模块
const ctrl = require('../controller/user.js')

// 用户请求注册页面
router.get('/register', ctrl.showRegisterPage)
// 用户请求登录页面
router.get('/login', ctrl.showLoginPage)
// 用户注册功能请求
router.post('/register'. ctrl.reg)
// 用户登录功能请求
router.post('/login', ctrl.login)

// 模块导出
module.exports = router
```



### 3.3  MVC 三层架构开发思想

​	将数据库的操作，单独提取出来

​	新建 db 文件夹，新建index.js 

```javascript
// 导入mysql模块，创建连接
const mysql = require('mysql')
const conn = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'node_item'
})

module.exports = conn
```



​	M: Model 数据库操作层

​	V: View 页面视图层

​	C: Controller 业务处理层

![mvc](images\mvc.jpg)



​	在 views 文件夹里面创建适合自己的模板页面，在页面中涉及到逻辑交互或者事件，发送异步请求

​	在 router 文件夹中 去挂载对应关系，去监听每一个请求，处理请求找到业务处理函数

​	在业务处理层 controller 中需要连接数据库， 那么再去请求 db 文件夹中的模块





---



## 4.0 Cookie 和 Session

​	Http 是无状态的，所以使用 Cookie，就是为了解决状态保持

### 4.1 Cookie 的基本使用

​	新建 COOKIE_STUDY 文件夹，叫什么名无所谓，新建 app.js

```javascript
// 1. 定义：cookie 就是存储在客户端的一小段文本
// 2. cookie 是一门客户端的技术，因为 cookie 是存储在客户端的
// 3. cookie 的作用，是为了实现 客户端与 服务器端之间的状态保持
// 4. cookie 技术不安全，不要使用它保持一些 敏感数据

const http = require('http')
const server = http.createServer()

server.on('request', (req, res) => {
    if(req.url === '/') {
      	// 每次客户端请求服务器，都去查看是否携带了cookie，并且看是什么
      	const cookie = {}
      	req.headers.cookie && req.headers.cookie.split('; ').forEach(item => {
          	const parts = item.split('=')
            cookie[parts[0]] = parts[1]
      	})
        
      	if(cookie.isvisit == 'yes') {
          	// 曾经访问过
          	res.writeHeader(200, {
              'Content-Type': 'text/html; charset=utf-8'
            })
            res.end('欢迎回来，送你一朵小红花')
      	} else {
          	// 第一次访问
          	res.writeHeader(200, {
              'Content-Type': 'text/html; charset=utf-8',
              'Set-Cookie': ['isvisit=yes', 'test=ook']
            })
            res.end('您还是头一回来这儿吧~')
      	}
        
  	} else {
      	res.end('404')
  	}
})

server.listen(4321, () => {
  	console.log('server running at http://127.0.0.1:4321')
})
```



### 4.2 使用 Cookie 的 expires 

​	cookie 默认只在浏览器页面关闭之后就失效了，如果想指定 cookie 的时效

​	需要指定 cookie 的 expires 属性

```javascript
// 设置 cookie
const expiresTime = new Date(Date.now() + 10 * 1000).toUTCString()
res.writeHeader(200, {
	'Content-Type': 'text/html; charset=utf-8',
	'Set-Cookie': ['isvisit=yes; expires=' + expiresTime, 'test=ook']
})
```



### 4.3 Session 的原理

​	

![Session](images\Session.png)



### 4.4 在项目中安装 express-session

​	

#### 步骤1：安装模块和导入

​	`npm i express-session -S`

```javascript
const session = require('express-session')
```



#### 步骤2：使用中间件

```javascript
// 启用 session 中间件
app.use(session({
  secret: 'keyboard cat', // 相当于一个加密钥匙，值可以是任意字符串
  resave: false, // 强制 session 保存到 session store 中
  saveUninitialized: false // 强制没有'初始化'的 session 保存到 storage 中
}))
```



#### 步骤3：保存到session

​	将私有的数据保存到当前请求的 session 会话中：

```javascript
// 将登录的用户保存到 session 中
req.session.user = result.dataValues
// 设置是否登录为 true
req.session.isLogin = true
```



#### 步骤4：清空数据的方法

​	通过 destroy() 方法清空 session 数据

```javascript
req.session.destroy(function(err) {
  	if(err) throw err
    console.log('用户退出成功')
    // 实现服务器的跳转，这个对比于客户端的跳转
    res.redirect('/')
})
```



### 4.5 黑马博客案例使用session

​	`npm i express-session -S`

​	打开 app.js

```javascript
// 导入session 模块
const session = require('express-session')

// 注册session中间件，只要能访问req，必然能访问 req.session 
app.use(session({
  	secret: '这是加密的密钥',
  	resave: false,
  	saveUninitialized: false
}))
```



​	找到 controller 文件夹，打开 user.js

```javascript
// 回到登录的请求函数
const login = (req, res) => {
  	// 1. 获取到表单中的数据
  	const body = req.body
    // 2. 执行sql语句，查询用户是否存在
    const sql1 = 'SELECT * FROM blog_users WHERE username = ? AND password = ?'
    conn.query(sql1, [body.username, body.password], (err, result) => {
      	if(err) return res.send({msg: '登录操作错误！', satus: 501})
        if(result.length !== 1) return res.send({msg: '登录失败，请重试！', satus: 502})
        
        // 把成功之后的用户信息，挂载到 session 上
        // 把用户登录成功的用户信息，也挂载到 session 上 result
        req.session.user = result[0]
        req.session.islogin = true
        
        // 登录成功
        res.send({msg: 'ok', status: 200})
    })
}
```



### 4.6 渲染首页

​	先操作 controller 文件夹中的 index.js

```javascript
// 在渲染首页的时候，携带参数回去
// 展示首页页面
const showIndexPage = (req, res) => {
    res.render('index.ejs', {
      	user: req.session.user,
      	islogin: req.session.islogin
    })
}

module.exports = {
  	showIndexPage
}
```





​	打开 views 文件夹，打开 index.ejs

```ejs
	<% if(islogin) { %>
		<!-- 用户名和注销按钮组 -->
        <div class="navbar-form nav navbar-nav navbar-right">
        	<button class="btn btn-warning">用户名 <strong> <%= user.nickname %> </strong></button>
            <button class="btn btn-danger">注销</button>
        </div>

        <ul class="nav navbar-nav navbar-right">
            <li class="dropdown">
            	<a href="#" class="dropdown-toggle" 
                   	data-toggle="dropdown" role="button" aria-haspopup="true"
                    aria-expanded="false">
                  	发表内容 <span class="caret"></span>
              	</a>
                <ul class="dropdown-menu">
                	<li><a href="#">文章</a></li>
                    <li><a href="#">问题</a></li>
                </ul>
            </li>
        </ul>
	<% } else { %>
		<!-- 注册和登录按钮组 -->
		<div class="navbar-form nav navbar-nav navbar-right">
			<a class="btn btn-success" href="/register">注册</a>
            <a class="btn btn-primary" href="/login">登录</a>
       	</div>
	<% } %>
```





---



## 5.0 实现注销功能



​	打开 views 文件夹中的 index.ejs ，找到注销按钮，改造为一个a标签

```html
<a class="btn btn-danger" href="/logout">注销</a>
```



​	打开 router 文件夹中的 user.js

```javascript
router.get('/logout', ctrl.logout)
```

​	

​	打开 controller 文件夹中 打开 user.js

```javascript
// 注销
const logout = (req, res) => {
  	req.session.destroy( () => {
      	// 使用 res.redirect 方法，可以让客户端重定向访问页面
      	res.redirect('/')
  	})
}
```





---



## 6.0 渲染文章添加页面



​	修改首页发表文章的链接地址去到一个新的页面

```html
<li><a href="/arcitle/add">文章</a></li>
```



​	在 router 文件夹中新建 article.js

```javascript
const express = require('express')
const router = express.Router()

// 监听客户端的 get 请求地址, 显示文章加载页面
router.get('/article/add', (req, res) => {
  	res.render('./article/add.ejs', {
      	user: req.session.user,
      	islogin: req.session.islogin
  	})
})

module.exports = router
```



​	在 views 文件夹中新建 article 文件夹，在文件夹中新建 add.ejs

```html
<html...>
```



### 6.1 抽离公共的模板部分

​	把页面中的头部和底部，抽离成为模板

​	在 views 文件夹中，新建 layout 文件夹，新建 header.ejs

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <link rel="stylesheet" href="/node_modules/bootstrap/dist/css/bootstrap.min.css">
    <script src="/node_modules/jquery/dist/jquery.min.js"></script>
    <!-- bootstrap 的js文件依赖 jquery 的功能 -->
    <script src="/node_modules/bootstrap/dist/js/bootstrap.min.js"></script>
</head>
<body>
    <!-- blog nav -->
    <nav class="navbar navbar-default">
        <div class="container">
            <div class="navbar-header">
                <a class="navbar-brand" href="/">黑马博客</a>
            </div>

            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
                <!-- 注册和登录按钮组 -->
                <div class="navbar-form nav navbar-nav navbar-right">
                    <a class="btn btn-success" href="/register">注册</a>
                    <a class="btn btn-primary" href="/login">登录</a>
                </div>
                <!-- 用户名和注销按钮组 -->
                <div class="navbar-form nav navbar-nav navbar-right">
                    <button class="btn btn-warning">用户名 <strong> *** </strong></button>
                    <button class="btn btn-danger">注销</button>
                </div>

                <ul class="nav navbar-nav navbar-right">
                    <li class="dropdown">
                        <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true"
                            aria-expanded="false">发表内容 <span class="caret"></span></a>
                        <ul class="dropdown-menu">
                            <li><a href="#">文章</a></li>
                            <li><a href="#">问题</a></li>
                        </ul>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
```

​	新建 footer.ejs

```html
    <!-- copyright -->
    <div class="text-center text-muted" style="margin-top: 50px;">黑马程序员 &copy; 杭州中心 2019</div>
    
</body>
</html>
```



​	在 views 文件夹的 index.ejs 中

```ejs
<%- include('./layout/header.ejs') %>

	<h1>文章列表</h1>

<%- include('./layout/footer.ejs') %>
```



​	在 article 文件夹 的 add.ejs 添加页面不需要自己写页面了

```ejs
<%- include('./layout/header.ejs') %>

	<h1>文章添加</h1>

<%- include('./layout/footer.ejs') %>
```

