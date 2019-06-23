# 管理后台登录

## 处理 HTML 中需要调整的地方

我们在点击登录的时候，发现登录按钮是直接跳转到`admin`目录下的`index.php`页面的，查看了一下原来是一个a标签，所以我们需要将登录按钮由 `a` 链接改为 `span` 标签，这样就不用在事件中阻止跳转

```html
<!--原来为：-->
<a href="index.php" class="btn btn-primary btn-block">登 录</a>
<!--修改为:-->
<span id="btn-login" class="btn btn-primary btn-block">登 录</span>
```



---

## 登录业务逻辑

用户第一次访问登录页面（GET），服务端会返回（响应）一个包含登录表单的 HTML 给浏览器。

当用户填写完表单点击登录按钮，浏览器会再发送一个 `POST` 请求到 `login.php` 文件。

我们需要在这个文件中处理 `POST` 请求，`login.php` 文件的处理逻辑大致如下：

```flow
s=>start: 开始
o1=>operation: 用户输入邮箱和密码
o2=>operation: 前端验证输入的合法性
c1=>condition: 是否合法
o3=>operation: ajax发送登录请求到后台
o4=>operation: 在后台对数据库进行条件查询
c2=>condition: 登录信息是否正确
o5=>operation: 使用session对登录状态进行保存
o6=>operation: 跳转到后台主页面
o7=>operation: 返回错误消息
o8=>operation: 提示用户
e=>end: 结束
s->o1->o2->c1
c1(yes)->o3
c1(no)->o8
o8(right)->o1
o3->o4->c2
c2(yes)->o5->o6->e
c2(no)->o7(right)->o8
```

### 先给登录按钮注册一个点击事件

```javascript
$(function(){
  // 给登录按钮注册点击事件
  $("#btn-login").on("click",function(){});
});
```

### 参数处理

在点击登录的登录的时候，我们需要对用户名和密码进行简单的验证，比如验证邮箱是否合理

```javascript
// 点击事件处理程序中的代码
// 1 收集用户的邮箱和密码
var email = $("#email").val();
var password = $("#password").val();
// 2 先给前端把数据做一下简单的数据验证
//  比如邮箱的格式是否正确
var reg = /\w+[@]\w+[.]\w+/;
if(!reg.test(email)){
  // 提示用户输入有误
  return;
}
```

我们在页面中有一个结构，用于提示用户一些错误信息

```html
<!-- 有错误信息时展示 -->
<div class="alert alert-danger" style="display: none;">
  <strong>错误！</strong><span id="msg">用户名或密码错误！</span>
</div>
```

所以当我们发现在验证邮箱有误的时候，可以提示用户

```javascript
// 1 收集用户的邮箱和密码
var email = $("#email").val();
var password = $("#password").val();
// 2 先给前端把数据做一下简单的数据验证
//  比如邮箱的格式是否正确
var reg = /\w+[@]\w+[.]\w+/;
if(!reg.test(email)){
  // 提示用户输入有误
  $("#msg").text("您输入的邮箱有误，请重新输入");//修改提示信息
  $(".alert").show();//显示错误区域
  return;
}
```

### 提交数据

当我们验证的数据已经合理之后，就可以把数据提交到服务端进行验证，在这个项目中我们使用ajax的方式进行提交

```javascript
// 点击事件处理程序中的代码
// 3 如果邮箱格式正确，就把数据发送回服务端
$.ajax({
  type: "POST",
  data : {email : email , password : password},
  url : "api/_userLogin.php",
  success : function(res){}
});
```

此时我们需要一个后台的php来处理这个请求，我们在`admin`目录下新建一个`api`文件夹，所有后台需要的接口我们都写在这里。在`api`文件夹下面新建一个`_userLogin.php`文件，我们在里面处理登录请求

```php
require_once '../../config.php';
require_once '../../functions.php';
// 1 获取邮箱和密码
  $email = $_POST["email"];
$password = $_POST["password"];
//2 根据邮箱和密码到数据库中查找有没有对应的数据
// 2.1 连接数据库
$connect = connect();
// 2.2 准备sql语句
$sql = "SELECT * FROM users WHERE email = '{$email}' and `password` = '{$password}' and `status` = 'activated'";
// 2.3 执行语句
$queryResult = query($connect,$sql);
// 3 判断查询的结果是不是由数据
$response = ["code"=>0,"msg"=>"操作失败"];
if($queryResult){
  $response["code"] = 1;
  $response["msg"] = "登录成功";
}
// 4 以json格式返回数据
header("content-type: application/json;charset=utf-8");
echo json_encode($response);
```

此时我们就可以处理登录请求了，回到`login.php`中继续完成ajax的代码

```javascript
$.ajax({
  type: "POST",
  data : {email : email , password : password},
  url : "api/_userLogin.php",
  success : function(res){
    // 4 判断返回的结果是否登录成功
    if(res.code == 1){
      //跳转到后台页面
      location.href = 'index.php';
    }else {
      $("#msg").text("用户名或密码错误！");
      $(".alert").show();
    }
  }
});
```

### 记住登录状态

登录虽然是能登录了，但是我们发现即使不登录，也能访问需要登录的页面，所以这个登录做的形同虚设，因此我们的记住登录的状态，然后在访问其他需要登录的页面的时候，验证一下是否已经登录，如果登录了才能访问，如果没有登录，就不能访问，而是帮用户跳转到登录页面进行登录。

因此需要在登录成功的时候使用session把登录状态记住

```php
// ... 省略了上面的查询的过程
// 3 判断查询的结果是不是由数据
$response = ["code"=>0,"msg"=>"操作失败"];
if($queryResult){
  // 3.1 如果登录成功了，应该把登录的状态记录一下
  session_start();//使用session记得一定要先开启
  $_SESSION['isLogin'] = 1;

  $response["code"] = 1;
  $response["msg"] = "登录成功";
}
// 4 以json格式返回数据
header("content-type: application/json;charset=utf-8");
echo json_encode($response);
```

**记得使用sessoin的时候，一定要把session的功能先开启:**   session_start();

然后我们在访问其他页面，比如`index.php`的时候，就可以判断是否真的已经登录了，只有登录了才能访问，否则直接跳转回到登录页面

判断是否登录的代码如下

```php
session_start();
// 先完成登录的验证 - 除了登录页面，都需要做登录的验证
// 1 没有isLogin这个key,有isLogin，但是值跟我们在登录的时候存储的不一样
if(!isset($_SESSION['isLogin']) || $_SESSION['isLogin'] != 1){
  // 如果没有登录过，就应该强制登录
  header("Location:login.php");
}
```

我们发现除了登录页面，其他页面都需要验证是否已经登录，所以我们可以把这段代码封装起来，其他页面想要使用的时候，直接调用即可。因此在`functions.php`中我们多封装一个函数

```php
/*
验证是否已经登录的函数的封装
*/
function checkLogin(){
  session_start();
  if(!isset($_SESSION['isLogin']) || $_SESSION['isLogin'] != 1){
    header("Location:login.php");
  }
}
```

以后在其他页面需要登录验证的时候，我们就可以使用这个函数快速的验证是否登录了