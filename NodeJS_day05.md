# NodeJS简要札记

---



## 1.0 模块加载机制



### 1.1 优先从缓存加载 

![require](images\require.png)

### 1.2 核心模块和用户模块

​	核心模块先去看缓存，没有的话再去加载到缓存

​	用户模块分文件扩展名加载，没有的话就报错





---



## 2.0 获取req参数 

​	分析不同的情况，得到的数据的方式是不一样的



#### 安装postman软件

​	用来测试请求是否发送成功，成功后获取到的相关数据有哪些

​	![postman](images\postman.jpg)



### 2.1 req.query获取查询参数

​	`npm init -y`

​	`npm i express -S`

```javascript
const express = require('express')
const app = express()

app.get('/user', (req, res) => {
    console.log(req.query)
    res.send('ok')
})

app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



### 2.2 req.params获取路径参数

```javascript
const express = require('express')
const app = express()

app.get('/user/:id/:name', (req, res) => {
    console.log(req.params)
    res.send('ok')
})

app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



### 2.3 req.body获取POST请求表单数据

​	`npm i body-parser -S`

```javascript
const express = require('express')
const app = express()

const bodyParser = require('body-parser')
app.use(bodyParser.urlencoded({extended: false}))

app.post('/user', (req, res) => {
    console.log(req.body)
    res.send('ok')
})

app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```





---



## 3.0 需求分析

后端项目运行地址：http://127.0.0.1:5000

前端项目运行地址：http://127.0.0.1:3000

### 3.1 介绍两种web开发模式

### 3.1.1 混合模式（传统开发模式）（黑马博客）

- 以后端程序员为主，基本上不需要前端程序员，或者，前端程序员只负责画页面、美化样式、写JS特效，前端程序员不需要进行数据的交互；

- 这种开发模式，在早些年比较常见；

- 传统开发模式下，用的最多的是 模板引擎  + Jquery  + Bootstrap

- 后端页面 .php   .jsp   .aspx   .cshtml

  总结：用数据填充模板并且渲染发生在**后端**

### 3.1.2 前后端分离（趋势）(英雄列表)

- 后端负责操作数据库、给前端暴露接口

- 前后端分离的好处：保证了各个岗位的职责单一；

- 前端负责调用接口，渲染页面、前端就可以使用一些流行的前端框架 Vue， React， Angular

  总结：用数据填充模板并且渲染发生在**前端**





### 3.2 项目技术栈

#### 3.2.1英雄列表

- 前端：jquery + art-template + semantic-ui
- 后端: express + mysql



#### 3.2.2黑马博客

- 前端：jquery + bootstrap
- 后端:  express + ejs + mysql

### 3.3 跨域

​	**浏览器**在给后端发送**ajax请求**的时候，如果触犯下列条件之一，就会涉及到跨域问题。

1. 协议不同
2. 域名不同
3. 端口不同



```javascript
$.ajax({
      url: 'https://www.zhihu.com/inbox',
      type: 'get',
      success: function(result) {
        // 如果成功拿到，测试弹个窗
        alert(result);
        // 后面再写个请求把数据发到自己服务器
      }
})

```





### 3.4 JSONP和CORS的区别

1. JSONP的原理：动态创建script标签；

- JSONP发送的不是Ajax请求
- 不支持 Post 请求；

2. CORS中文意思是`跨域资源共享` ,需要服务器端进行 `CORS` 配置；

- CORS 发送的是真正的Ajax请求；
- CORS 支持Ajax的跨域；
- 如果要启用 CORS 跨域资源共享，关键在于 服务器端，只要 服务器支持CORS跨域资源共享，则 浏览器肯定能够正常访问 这种 CORS 接口；而且，客户端在 发送 Ajax的时候，就像发送普通AJax一样，没有任何代码上的变化；

3. 对于Node来说，如果想要开启 CORS 跨域通信，只需要安装`cors`的模块即可；



### 3.5 设计数据库heros表

​	id为主键自动递增

​	name 字符类型，不为空

​	gender 字符类型，不为空

​	ctime 字符类型，不为空

​	isdel  tinyint类型，默认值为0



---



## 4.0 设计接口

​	创建 API_server 文件夹，下载 express 模块，运行在5001端口



### 4.1 创建后端API项目基本结构

```javascript
const express = require('express')
const app = express()

app.get('/', (req, res) => {
    res.send('后台数据接口访问成功')
})

app.listen(3000, () => {
    console.log('server running at http://127.0.0.1:3000')
})
```



### 4.2 获取所有英雄接口

​	`npm i mysql -S`

```javascript
// 导入数据库模块，创建数据库连接
const mysql = require('mysql')
const conn = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'node_item'
})

// 设计获取请求所有的英雄接口
app.get('/getAllHeros', (req, res) => {
    // 4.1 编辑sql语句
    let sqlStr = 'SELECT * FROM heros'
    conn.query(sqlStr, (err, result) => {
        if (err) res.send({ 
            status: 500, 
            msg: err.message, 
            data: null })
        // 4.2 返回请求的数据
        res.send({ 
            status: 200, 
            msg: 'ok', 
            data: result 
        })
    })
})
```



### 4.3 添加英雄的接口

```javascript
// 导入解析表单post提交时传输的数据
const bodyParser = require('body-parser')
// 使用中间件
app.use(bodyParser.urlencoded({ extended: false }))

// 设计添加英雄的接口，获取表单post提交时的数据并操作到数据库中
app.post('/addheros', (req, res) => {
    let hero = req.body
    // 创建时间并格式化数据，添加在req.body中
    let dt = new Date()
    let y = (dt.getFullYear() + '').padStart(2, '0')
    let m = (dt.getMonth() + 1).toString().padStart(2, '0')
    let d = dt.getDate().toString().padStart(2, '0')
    let hh = dt.getHours().toString().padStart(2, '0')
    let mm = dt.getMinutes().toString().padStart(2, '0')
    let ss = dt.getSeconds().toString().padStart(2, '0')
    hero.ctime = y + '-' + m + '-' + d + ' ' + hh + ':' + mm + ':' + ss
    // 编辑sql语句并执行
    let sql = 'insert into heros set ?'
    conn.query(sql, hero, (err, result) => {
        if (err) res.send({
            status: 500,
            msg: err.message,
            data: null
        })

        res.send({
            status: 200,
            msg: 'ok',
            data: null
        })
    })

})
```



### 4.4 根据id查询英雄的接口



```javascript
// 根据id获取英雄的信息
app.get('/gethero/:id', (req, res) => {
    let id = req.params.id
    let sql = 'SELECT * FROM heros WHERE id = ?'
    conn.query(sql, id, (err, result) => {
        if (err) res.send({ 
            status: 500, 
            msg: err.message, 
            data: null })
        // 4.2 返回请求的数据
        res.send({ 
            status: 200, 
            msg: 'ok', 
            data: result 
        })
    })
})
```





### 4.5 根据id更新英雄的接口

```javascript
// 根据id更新英雄的信息
router.post('/updatehero/:id', (req, res) => {
        let id = req.params.id
        let newInfo = req.body
        let sql = 'update heros set ? where id = ?'
        conn.query(sql, [newInfo, id], (err, result) => {
            if (err) res.send({
                status: 500,
                msg: err.message,
                data: null
            })
            res.send({
                status: 200,
                msg: 'ok',
                data: {
                    affectedRows: result.affectedRows
                }
            })
        })
    }
```





### 4.6 根据id软删除英雄的接口



```javascript
// 根据id对英雄进行软删除
router.get('/deletehero/:id', (req, res) => {
        let id = req.params.id
        let sql = 'update heros set isdel = 1 where id = ?'
        conn.query(sql, id, (err, result) => {
            if (err) res.send({
                status: 500,
                msg: err.message,
                data: null
            })
            res.send({
                status: 200,
                msg: 'ok',
                data: {
                    affectedRows: result.affectedRows
                }
            })
        })
    })
```





## 5.0 封装API_SERVER项目



### 5.1 服务模块

​	server.js

```javascript
// 导入express模块创建服务器
const express = require('express')
const app = express()
// 导入解析表单post提交时传输的数据
const bodyParser = require('body-parser')
// 使用中间件
app.use(bodyParser.urlencoded({ extended: false }))

// 导入 router 模块
const router = require('./router.js')
// 使用app.use注册功能
app.use(router)

// 启动服务器，运行在5001端口
app.listen(5001, () => {
    console.log('server running at http://127.0.0.1:5001')
})
```



### 5.2 路由模块

```javascript
// 导入express模块，创建路由对象
const express = require('express')
const router = express.Router()
// 引入函数集模块
const ctls = require('./functions.js')

// 监听客户端根目录请求，返回基本数据
router.get('/', ctls.testFn)
// 设计获取请求所有的英雄接口
router.get('/getallheros', ctls.getAllHeros)
// 设计添加英雄的接口，获取表单post提交时的数据并操作到数据库中
router.post('/addheros', ctls.addHero)
// 根据id获取英雄的信息
router.get('/gethero/:id', ctls.getHeroById)
// 根据id更新英雄的信息
router.post('/updatehero/:id', ctls.updateById)
// 根据id对英雄进行软删除
router.get('/deletehero/:id', ctls.deleteById)

// 向外暴露路由对象
module.exports = router
```





### 5.3 函数集

```javascript
const conn = require('./db.js')

// 直接暴露出去处理任务的函数
module.exports = {
    testFn: (req, res) => {
        res.send('请求后台接口成功~')
    },
    getAllHeros: (req, res) => {
        // 4.1 编辑sql语句
        let sqlStr = 'SELECT * FROM heros'
        conn.query(sqlStr, (err, result) => {
            if (err) res.send({ 
                status: 500, 
                msg: err.message, 
                data: null })
            // 4.2 返回请求的数据
            res.send({ 
                status: 200, 
                msg: 'ok', 
                data: result 
            })
        })
    },
    addHero: (req, res) => {
        let hero = req.body
        // 创建时间并格式化数据，添加在req.body中
        let dt = new Date()
        let y = (dt.getFullYear() + '').padStart(2, '0')
        let m = (dt.getMonth() + 1).toString().padStart(2, '0')
        let d = dt.getDate().toString().padStart(2, '0')
        let hh = dt.getHours().toString().padStart(2, '0')
        let mm = dt.getMinutes().toString().padStart(2, '0')
        let ss = dt.getSeconds().toString().padStart(2, '0')
        hero.ctime = y + '-' + m + '-' + d + ' ' + hh + ':' + mm + ':' + ss
        // 编辑sql语句并执行
        let sql = 'insert into heros set ?'
        conn.query(sql, hero, (err, result) => {
            if (err) res.send({
                status: 500,
                msg: err.message,
                data: null
            })
    
            res.send({
                status: 200,
                msg: 'ok',
                data: null
            })
        })
    
    },
    getHeroById: (req, res) => {
        let id = req.params.id
        let sql = 'SELECT * FROM heros WHERE id = ?'
        conn.query(sql, id, (err, result) => {
            if (err) res.send({ 
                status: 500, 
                msg: err.message, 
                data: null })
            // 4.2 返回请求的数据
            res.send({ 
                status: 200, 
                msg: 'ok', 
                data: result 
            })
        })
    },
    updateById: (req, res) => {
        let id = req.params.id
        let newInfo = req.body
        let sql = 'update heros set ? where id = ?'
        conn.query(sql, [newInfo, id], (err, result) => {
            if (err) res.send({
                status: 500,
                msg: err.message,
                data: null
            })
            res.send({
                status: 200,
                msg: 'ok',
                data: {
                    affectedRows: result.affectedRows
                }
            })
        })
    },
    deleteById: (req, res) => {
        let id = req.params.id
        let sql = 'update heros set isdel = 1 where id = ?'
        conn.query(sql, id, (err, result) => {
            if (err) res.send({
                status: 500,
                msg: err.message,
                data: null
            })
            res.send({
                status: 200,
                msg: 'ok',
                data: {
                    affectedRows: result.affectedRows
                }
            })
        })
    }
}
```



### 5.4 数据库连接模块

```javascript
// 导入数据库模块，创建数据库连接
const mysql = require('mysql')
const conn = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'node_item'
})

module.exports = conn
```





---



## 6.0 安装semantic-ui



### 6.1 全局安装Gulp

​	`npm i gulp -g`

![downGulp](images\downGulp.jpg)



### 6.2 初始化项目

​	`npm init -y`

![web-init](images\web-init.jpg)



### 6.3 下载semantic-ui

​	`npm i semantic-ui -S`

![autosemantic](images\autosemantic.jpg)



### 6.4 进程操作

​	![semantic-yes](images\semantic-yes.jpg)



![semantic-ok](images\semantic-ok.jpg)



![semantic-all](images\semantic-all.jpg)



### 6.5 使用gulp工具完成操作

​	`cd semantic`

​	`gulp build`

![semantic-gulp](images\semantic-gulp.jpg)

