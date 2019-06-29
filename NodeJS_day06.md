> # NodeJS简要札记

---



## 1.0 在项目中配置和使用semantic-ui



​	在项目中配置 semantic-ui ，其实就是让 views 目录里面的html文件引入外面文件夹的文件

​	如果是直接在 views 里面，由于托管了，所以不需要再设置什么

```javascript
const express = require('express')
const app = express()

// 托管 views 文件夹下的文件
app.use(express.static('./views'))
// 托管 semantic 文件夹里的静态资源
app.use('/semantic', express.static('./semantic'))
app.use('/node_modules', express.static('./node_modules'))

app.listen(3001, () => {
    console.log('server running at http://127.0.0.1:3001')
})
```



#### 说明

​	将 views 文件夹托管了，那么在访问页面的时候

​	本应该是 `127.0.0.1:3001/views/index.html`

​	现在访问方式 `127.0.0.1:3001`

​	原因是托管出去的文件夹，在请求文件的时候，语法不允许再写这个文件夹



​	而在 index.html 中需要引入以下三个文件

```html
    <link rel="stylesheet" href="/semantic/dist/semantic.min.css">
    <script src="/node_modules/jquery/dist/jquery.min.js"></script>
    <!-- semantic 文件依赖jquery -->
    <script src="/semantic/dist/semantic.min.js"></script>
```

​	这三个文件，有两个在 semantic 文件夹，一个在 node_modules 文件夹

​	如果需要载入文件，则必须要将这两个文件夹也托管出去

​	！！！但是，但是，但是

​	上面说，托管出去之后，语法不允许在请求路径中带文件夹名称

​	也就是说，当引入一个文件的时候应该 `<link rel="stylesheet" href="/dist/semantic.min.css">`

​	解释： 以上引入文件的时候，没有带 /semantic

​	这样的话，在引入的时候，编辑器就没有路径提示了，一般这样情况下，极容易自己写错



​	！！！！所以，所以，所以

​	在托管出去的时候，加了一个虚拟路径，这样在编辑器引入的时候前面加上文件夹名称，就有提示了

​	真好~



### 1.1 通过ajax请求获取服务器的数据



​	__1，启动 phpstudy 中的mysql服务__

​	__2，运行后端项目__

​	__3，运行前端项目__



​	在 views 文件夹新建js文件，创建ajax请求

```javascript
	// 获取英雄列表
    function getAllHeros() {
        $.ajax({
            type: 'get',
            url: 'http://127.0.0.1:5001/getallheros',
            success: function(res) {
                var html = template('rows', res)
                $("#tbd").html(html)
            }
        })
    }

    getAllHeros()
```



​	**此时出现了跨域的报错信息**

​	那么需要在 **后端项目** 的 server.js 中下载 cors 模块，并且导入

​	`npm i cors -S`

```javascript
// 导入cors模块
const cors = require('cors')
app.use(cors())
```





### 1.2 完成英雄列表的渲染

​	在html中，使用 semantic 的表格，和 art-template 模板渲染数据

​	`npm i art-template -S`

​	引入 art-template 模板文件

```html
    <h1>英雄列表</h1>
    <button class="ui primary button" id="add">添加英雄</button>

    <table class="ui celled striped blue table">
        <thead>
            <tr>
                <th>编号</th>
                <th>英雄名称</th>
                <th>性别</th>
                <th>创建时间</th>
                <th>操作</th>
            </tr>
        </thead>
        <tbody id="tbd"></tbody>
    </table>

    <script type="text/template" id="rows">
        {{ each data }}
            <tr>
                <td>
                    <div class="ui ribbon label {{ $value.isdel ? 'red' : 'blue' }}">
                        {{ $value.isdel ? '删除' : '正常' }}
                    </div>
                    {{ $value.id }}
                </td>
                <td>{{ $value.name }}</td>
                <td>{{ $value.gender }}</td>
                <td>{{ $value.ctime }}</td>
                <td>
                    <a href="javascript:;">编辑</a>
                    <a href="javascript:;">删除</a>
                </td>
            </tr>
        {{ /each }}
    </script>
```



### 1.3 编辑添加英雄页面

​	给上面的 添加英雄 按钮加一个id，当触发点击事件的时候，显示以下的弹出层

```javascript
	// 触发弹出层
    $("#add").on('click', function() {
        $('.ui.modal').modal('show')
    })
    // 激活下拉框样式和功能
    $('.ui.dropdown').dropdown()
```

​	弹出层结构

```html
    <div class="ui tiny modal">
        <i class="close icon"></i>
        <div class="header">
            添加英雄信息
        </div>
        <div class="content">
            <form class="ui form" id="userInfo">
                <div class="field">
                    <label>名称：</label>
                    <input type="text" name="name" placeholder="姓">
                </div>
                <div class="field">
                    <label>性别：</label>
                    <select name='gender' class="ui fluid dropdown">
                        <option value="男">男</option>
                        <option value="女">女</option>
                    </select>
                </div>
            </form>
        </div>
        <div class="actions">
            <div class="ui black deny button">
                取消
            </div>
            <div class="ui positive right button" id="addBtn">
                添加
            </div>
        </div>
    </div>
```



### 1.4 完成添加英雄功能

​	当触发按钮的点击事件，设置 form 表单序列化数据

```javascript
	// 点击按钮添加英雄
    $("#addBtn").on('click', function() {
        $.ajax({
            type: 'post',
            url: 'http://127.0.0.1:5001/addheros',
            data: $("#userInfo").serialize(),
            success: function(res) {
                if(res.status === 200) {
                    getAllHeros()
                }
            }
        })
    })
```



​	当数据请求成功后，前端会重新查询数据，发起数据更新

​	基本功能完成，编辑和删除功能略.





---



## 2.0 黑马博客项目



### 2.1 初始化项目的基本结构

​	创建 HEIMA_BLOG 文件夹，叫什么名称无所谓

​	`npm init -y`

​	`npm i express -S`

​	`npm i ejs -S`

```javascript
// 导入模块并创建服务器
const express = require('express')
const app = express()

// 使用模板渲染数据
app.set('view engine', 'ejs')
app.set('views', './views')

// 监听客户端根目录请求
app.get('/', (req, res) => {
    res.render('index.ejs', {})
})

app.listen(80, () => {
    console.log('server running at http://127.0.0.1:80')
})
```



### 2.2 托管 node_modules 资源目录

​	在项目中，新建 views 文件夹，新建 index.ejs ，初始化基本的骨架结构

​	使用 bootstrap 和 jquery

​	`npm i bootstrap@3.3.7 -S`

​	`npm i jquery -S`

​	以上述 **说明** 那一条的方式，托管目录，并在页面中，引入文件

```javascript
// 托管静态文件
app.use('/node_modules', express.static('./node_modules'))
```

​	在index.ejs中引入

```html
    <link rel="stylesheet" href="/node_modules/bootstrap/dist/css/bootstrap.min.css">
    <script src="/node_modules/jquery/dist/jquery.min.js"></script>
    <!-- bootstrap 的js文件依赖 jquery 的功能 -->
    <script src="/node_modules/bootstrap/dist/js/bootstrap.min.js"></script>
```



### 2.3 使用 bootstrap 搭建首页结构

​	复制CSS组件中的导航组件，改写成想要的样式

```html
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

    <div class="container">
        <h1>文章列表</h1>
    </div>

    <!-- copyright -->
    <div class="text-center text-muted" style="margin-top: 50px;">黑马程序员 &copy; 杭州中心 2019</div>
```



### 2.4 处理访问注册和登录页

​	注册页面和用户页面不需要自己写，直接复制过来就可以

​	在首页中，把注册和登录的按钮改造成a链接，分别给加上href属性指向不同的路径



​	在后端处理对应的请求

```javascript
// 监听客户端注册请求
app.get('/register', (req, res) => {
    res.render('./user/register.ejs', {})
})
// 监听客户端登录请求
app.get('/login', (req, res) => {
    res.render('./user/login.ejs', {})
})
```



### 2.5 创建mysql数据表并完成功能

​	

#### 步骤1：创建对应的表

​	在数据库中创建对应的表

​	id   =》 int类型， 主键 ，自动递增

​	username  =》 varchart类型， 不为null

​	password   =》 varchart类型， 不为null

​	nickname  =》 varchart类型， 不为null

​	ctime  =》 varchart类型， 不为null

​	isdel  =》 tinyint类型， 不为null

![heima_sql](images\heima_sql.jpg)



#### 步骤2：创建连接对象

​	在服务模块文件中，下载 mysql 模块并导入

​	`npm i mysql -S`

```javascript
// 导入mysql模块，创建连接
const mysql = require('mysql')
const conn = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: 'root',
    database: 'node_item'
})
```



#### 步骤3：解析post提交的数据

​	下载 body-parser 获取和解析post提交的数据

​	`npm i body-parser -S`

```javascript
// 注册中间件，获取post请求时表单的数据
const bodyParser = require('body-parser')
app.use(bodyParser.urlencoded({extended: false}))
```



#### 步骤4：前端发送请求

​	在 views 文件里面的 user 文件夹中，找到 register.js

​	底部引入 jquery，创建请求

```html
  <script src="/node_modules/jquery/dist/jquery.min.js"></script>
  <script>
    $(function () {
      $('#form').on('submit', function (e) {
        // 取消表单的默认提交事件
        e.preventDefault()
        // 当取消了表单的默认提交行为之后，手动发起Ajax请求，提交表单
        $.ajax({
          type: 'POST',
          url: '/register',
          data: $('#form').serialize(),
          dataType: 'json',
          success: function (result) {
            console.log(result)
          }
        })
      })
    })
  </script>
```



​	由于 form 表单有自身的 action 和 method，常用于提交请求，所以即使不写，也会默认闪一下

​	而此时ajax发送会不成功，所以需要调用 `e.preventDefault()` 用来解决阻止默认的行为



#### 步骤5：后端处理 post 请求

​	通过接收到数据，判断是否有重复的用户名，如果没有重复的用户名，还要服务端添加创建时间

​	`npm i moment -S`

```javascript
// 导入处理时间的模块
const moment = require('moment')
```



​	处理请求：

```javascript
// 监听的是前端的post注册请求
app.post('/register', (req, res) => {
    let body = req.body
    // 验证表单数据是否符合规则
    if (body.username.trim().length <= 0 || body.password.trim().length <= 0 || body.nickname.trim().length <= 0) {
        return res.send({ msg: '请填写完整的数据', status: 501 })
    }
  
    // 查询用户名是否重复
    let sql1 = 'select count(*) as count from blog_users where username=?'
    conn.query(sql1, body.username, (err, result) => {
        if (err) return res.send({ msg: '查重操作失败', status: 502 })
        if (result[0].count !== 0) return res.send({ msg: 'sorry，用户名重复了', status: 503 })
        
        // 执行注册的业务逻辑
        body.ctime = moment().format('YYYY-MM-DD HH:mm:ss')
        let sql2 = 'insert into blog_users set ?'
        conn.query(sql2, body, (err, result) => {
            if (err) return res.send({ msg: '注册操作失败！', status: 504 })
            if (result.affectedRows !== 1) return res.send({  msg: '注册功能失败！', status: 505 })
            res.send({ msg: '恭喜亲，注册用户名成功！', status: 200 })
        })
    })
})
```





