# AJAX简要札记

---

$$
有了ajax，前端有了一道分水岭
$$

## 1.0 关于ajax和异步



#### ajax是什么   axios

​	Ajax 即“Asynchronous Javascript And XML”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发技术



​	关键词： __异步，不需要页面跳转__，__局部刷新__



`__本质： 基于http协议以异步的方式通过 XMLHttpRequest对象 向服务器进行通信__`

`__作用： 在页面不刷新的情况下，请求服务器，实现局部的渲染数据__`



#### 什么是异步

​	异步的反义词就是同步，同步指在网页的一次请求中，如果不完成这次请求，就不会响应其他的任何操作，效率比较慢；

##### 通过后台处理页面跳转

```php
/*  
	以往方式写出来的页面，是静态的，数据没有办法改变和刷新
	使用php书写：
		a, 所有要操作的文件后缀名改成php, 或者通过提交按钮和a链接跳转到php文件
		b, 参数必须拼在a链接后面或者form表单内部
		b, 书写符合php语法的代码，方法较多难以记忆
		c, 在页面中来来回回的跳转, 发送的请求要等待完成之后才能做其他的操作
*/
```

![old-request](images\old-request.jpg)



前端的register.html文件

```html
<form action="./register.php" method="post">     
	<span id="msg">aaa</span>  
    用户名：<input type="text" id="userName" name="username" >
    昵称：<input type="text" name="nickname" >
    密码： <input type="password" name="password" >   
    <input type="submit" value="注册">
</form>
```

后台处理的php文件

```php
<?php 
    header('content-type:text/html;charset=utf-8');
    if($_SERVER['REQUEST_METHOD'] == 'POST'){
        //获取用户名
        $name=$_POST['username'];
        //判断数据库中是否已有这个用户名
        $names=['jack','rose','tom','lili'];
        //有则注册失败，否则成功
        if(in_array($name,$names)){
            $str = '这个名字太火了，换一个吧！';
            echo $str;
            header("refresh:10;url=register.html");
        }else{
            $str = '恭喜，名字可用！';
            echo $str;
            header("refresh:2;url=register.html");
        }
    }
?>
```



##### 通过前端发送ajax请求

​	异步就是在发起请求之后，不用理会这个过程，等请求结束了，自动返回回来数据，不用进行等待；

```php
/*
	a, 原先是html的文件还是html，不用修改文件类型
	b, 发送请求不再仅仅依靠a链接或者表单元素
	c, 不需要来回跳转页面，用户不需要进行等待
	d, 返回的数据可以在局部进行刷新
*/
```



![ajax](images\ajax.jpg)



前端的register.html文件

```html
<form>     
    <span id="msg">aaa</span>  
    用户名：<input type="text" name="username" id="username">
    昵称：<input type="text" name="nickname" >
    密码： <input type="password" name="password" >   
    <input type="submit" value="注册">
</form>

<!-- 显示ajax请求返回回来的数据 -->
<div class="showmsg"></div>
```



前端基于http发送的一个异步请求

```javascript
        document.querySelector("#username").onblur = function(){
            // 1.获取用户数据
            var name = this.value;

            // 2.1 创建异步对象
            var xhr = new XMLHttpRequest();
            // 2.2 请求行发送 open(请求方式，请求url+键值对的参数):
            xhr.open("get","validate.php?username="+name);
          	// 2.3 get请求没有请求头
            // 2.4 请求体:发送请求 
            xhr.send(null);

      		// 2.5 通过readystate发生改变的事件处理响应
            xhr.onreadystatechange = function(){
              	// 2.6 判断请求是否结束后成功返回   判断服务器状态码是否为200
                if(xhr.readyState == 4 && xhr.status == 200){
                  	// 通过xhr对象的responseText接收返回的数据，写入盒子内
                    document.querySelector(".showmsg").innerHTML = xhr.responseText;;
                }
            }
        };
```



处理后端的php文件

```php
<?php 
    header('content-type:text/html;charset=utf-8');
    if($_SERVER['REQUEST_METHOD'] == 'GET'){
        //获取用户名
        $name=$_GET['username'];
        //判断数据库中是否已有这个用户名
        $names=['jack','rose','tom','lili'];
        //有则注册失败，否则成功
        if(in_array($name,$names)){
            $str = '这个名字太火了，换一个吧！';
            echo $str;
            // header("refresh:10;url=register.html");
        }else{
            $str = '恭喜，名字可用！';
            echo $str;
            // header("refresh:2;url=register.html");
        }
    }
?>
```



#### 前端的get请求

```php
// 1.1 创建异步对象
var xhr = new XMLHttpRequest();
// 1.2 请求行发送 open(请求方式，请求url+键值对的参数):
xhr.open("get","validate.php?username="+name); // url地址 ? 键=值 & 键=值 & 键=值
// 1.3 get请求没有请求头
// 1.4 请求体:发送请求 
xhr.send(null);

// 1.5 通过readystate发生改变的事件处理响应
xhr.onreadystatechange = function(){
    // 1.6 判断请求是否结束后成功返回   判断服务器状态码是否为200
    if(xhr.readyState == 4 && xhr.status == 200){
        // 通过xhr对象的responseText接收返回的数据，写入盒子内
        document.querySelector(".showmsg").innerHTML = xhr.responseText;
    }
}
```



#### 前端的post请求

```php
// 1.1 创建异步对象
var xhr = new XMLHttpRequest();
// 1.2 设置请求行 open(请求方式，请求url)
// post请求不需要拼接参数
xhr.open("post","validate.php");
// 1.3 设置请求头:setRequestHeader()
xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
// 1.4 设置请求体 send()
// post的参数在这个函数中设置(如果有参数)
xhr.send("username="+name);

// 1.5 通过readystate发生改变的事件处理响应
xhr.onreadystatechange = function(){
    // 1.6 判断请求是否结束后成功返回   判断服务器状态码是否为200
    if(xhr.readyState == 4 && xhr.status == 200){
        // 通过xhr对象的responseText接收返回的数据，写入盒子内
        document.querySelector(".showmsg").innerHTML = xhr.responseText;
    }
}
```



关于get请求和post的请求区别是什么

1、GET没有请求主体，使用xhr.send(null)
2、GET可以通过在请求URL上添加请求参数
3、POST可以通过xhr.send('name=itcast&age=10')
4、POST需要设置    Content-type:application/x-www-form-urlencoded



---




## 2.0 使用异步对象发送读取JSON文件

​	在项目开发过程中，用的最多的方式就是json

#### json格式的数据和特点

```json
[
    {
       "src":"./images/nav_1.png" ,
       "text":"京东超市"
    },
    {
        "src":"./images/nav_2.png" ,
        "text":"全球购物"
     },
     {
        "src":"./images/nav_3.png" ,
        "text":"京东市场"
     }
]
```



##### 规则和特点

| 关于json的描述                       |
| ------------------------------- |
| 1，一组花括号表示一个对象，一个对象通过键值对写入一堆相关数据 |
| 2，一组方括号表示一个数组，多组对象通过数组的方式装载     |
| 3，对象的所有属性都必须加上双引号，值没有undefined  |
| 4，文件后缀名为.json，json格式的数据内不允许写注释  |



##### 操作json的方法

| 前端操作json的方式            |                         |
| ---------------------- | ----------------------- |
| JSON.parse(json字符串)    | 将json格式的字符串转换为数组或者对象    |
| JSON.stringify(对象或者数组) | 将字面量对象或者数组转换为json格式的字符串 |

| php操作json的方式         |                          |
| -------------------- | ------------------------ |
| json_decode(json字符串) | 将json格式的字符串转换为php的数组或者对象 |
| json_encode(关联数组)    | 将php的数组转换为json字符串        |



#### 关于json的操作

​	拿到数据之后，大部分情况下是一个装了很多数据的数组，里面是一个个对象

所以需要对数组进行循环遍历

通过for循环，拿到每一个对象，通过在循环中，拿到当前对象某个属性对应的值

`data[i].src`       `data[i]["src"]`

例如：

```javascript
xhr.onreadystatechange = function(){
	if(xhr.status == 200 && xhr.readyState == 4){
		var result = xhr.responseText;
        // 通过操作json的方法获取到data数组
        var data = JSON.parse(result);
		
		var arr = [];
        for(var i=0;i<data.length;i++){
        	var str = "<li>"
            		+ '<a href="#">'
            		+ '<img src="' + data[i].src + '" alt="">'
            		+ '<p>' + data[i].text + '</p>'
            		+ '</a>'
            		+ '</li>';
            arr.push(str)    
		}
        // 将生成的页面结构添加到dom元素中
		document.querySelector("ul").innerHTML = arr.join("");
	}
}
```



后端处理php文件的代码

```php
<?php
    echo file_get_contents("../data/nav.json");
?>
```



---



## 3.0 xml语法和文件操作

​	以前比较频繁的数据传输方式就是xml的文件格式，它是html的一个扩展

#### xml文件格式的创建

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 上面这一句必须是整个xml文档的第一句，否则格式错误 -->
    <root>
        <!-- 标签名称 以字母和下划线开头，不能有空格，不能包含特殊字符，区分大小写 -->
        <items>
            <!-- 说明下面的内容需要描述为数组 -->
            <item>
                <!-- 描述数组中的具体的成员值 -->
                <src>./images/nav_1.png</src>
                <text>京东超市</text>
            </item>
            <item>
                <!-- 描述数组中的具体的成员值 -->
                <src>./images/nav_2.png</src>
                <text>全球购物</text>
            </item>
            <item>
                <!-- 描述数组中的具体的成员值 -->
                <src>./images/nav_3.png</src>
                <text>京东市场123</text>
            </item>
        </items>
    </root>
```



##### xml文件的规则和特点

| 关于xml数据格式的规则                 |
| ---------------------------- |
| 1，文件后缀名必须得为.xml格式            |
| 2，必须得在头部声明那样一句话，相当于doctype声明 |
| 3，内容都以双标签包裹，可以嵌套和并列，不能交叉     |
| 4，标签名称以字母和下划线开头，叫什么，多长的名称无所谓 |



#### 关于xml的操作

前端获取xml文件的内容

​	在响应成功后，通过responseXML来接收当前的数据，会拿到一长串标签

​	此时可以使用js中选择器，选择对应的标签 xhr.responseXML.querySelector("标签名称")

​	如果获取到的是多个元素，则需要遍历，也是可以通过获取元素内的数据传递给其他标签

```javascript
xhr.onreadystatechange = function(){
	if(xhr.status == 200 && xhr.readyState == 4){
		// responseText：接收普通字符串 responseXML：专门用来接收服务器返回的xml数据的
		var result = xhr.responseXML;
		// 将获取到的数据生成动态的页面结构
		var items = result.querySelectorAll("item");

		var arr = [];
        for(var i=0;i<items.length;i++){
          	// 获取到标签内部的数据
          	var src = items[i].querySelector("src").innerHTML;
            var text = items[i].querySelector("text").innerHTML;
        	var str = "<li>"
            		+ '<a href="#">'
            		+ '<img src="' + src + '" alt="">'
            		+ '<p>' + text + '</p>'
            		+ '</a>'
            		+ '</li>';
            arr.push(str)    
		}
        // 将生成的页面结构添加到dom元素中
		document.querySelector("ul").innerHTML = arr.join("");
	}
}
```



后端php文件处理xml文件的请求

```php
<?php
    // 默认返回数据的类型
    // header("Content-Type:text/html;charset=utf-8");
    // 如果需要返回数据为xml,那么标准的响应头的写法应该是
    header("Content-Type:application/xml;charset=utf-8");
    echo file_get_contents("../data/nav.xml");
?>
```



---



## 4.0 改进和封装工具

$$
封装的目的是为了方便更高效的使用，一次封装多次调用
$$

#### 代码的封装

​	关于代码的封装，重点在于要知道怎么使用，具体的封装过程等到了一定的开发经验再把玩

```javascript
var dilireba = {
    // 将{"name":"jack","age":20} 的参数要转换为 ?name=jack&age=20
    getpa:function(data){
        if(data && typeof data == "object"){
            var str = "?";
            for(var key in data){
                str = str + key + "=" + data[key] + "&";
            }
            str = str.substr(0,str.length-1);
        }
        return str;
    },
    ajax:function(option){
        // 接收用户参数进行相应处理
        var type = option.type || 'get';
        // location.href 可以获取当前文件的路径 
        var url = option.url || location.href;
        // 接收参数:在js中最方便收集的数据类型为对象，所以我们就规定传递的参数必须是对象
        var data = this.getpa(option.data) || "";
        // 响应成功之后的回调函数 => 这个函数一般就是处理字符拼接，标签渲染的函数
        var success = option.success;
        
      	// 创建异步对象
        var xhr = new XMLHttpRequest();
        // 请求行  如果是get请求就需要拼接参数
        if(type == "get"){
            url += data;
            data = null; // 拼接后设置data为null，在send()方法中就不会再次发送
        }
        xhr.open(type,url);
        // 请求头  如果是post才需要请求头
        if(type == "post"){
            xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
        }
        // 请求体
        xhr.send(data);
        // 让异步对象接收响应
        xhr.onreadystatechange = function(){
            // 一个成功的响应有两个条件：1.服务器成功响应 2.数据解析完毕可以使用
            if(xhr.status == 200 && xhr.readyState == 4){
                // 接收响应的返回值 responseText   responseXML
                var rh = xhr.getResponseHeader("Content-Type");
                if(rh.indexOf("xml") != -1){
                    var result = xhr.responseXML;
                }
                else if(rh.lastIndexOf("json") != -1){
                    var result = JSON.parse(xhr.responseText);
                }else{
                    var result = xhr.responseText;
                }
                // 接收数据之后，调用回调函数
                success && success(result)
            }
        }
    },
}
```



#### 封装代码的使用

```javascript
dilireba.ajax({
    type:'post',
    url:'./server/nav-json.php',
    data:{},
    success: function (result){
        var arr = [];
        for(var i=0;i<data.length;i++){
        	var str = "<li>"
            		+ '<a href="#">'
            		+ '<img src="' + data[i].src + '" alt="">'
            		+ '<p>' + data[i].text + '</p>'
            		+ '</a>'
            		+ '</li>';
            arr.push(str)    
		}
        // 将生成的页面结构添加到dom元素中
		document.querySelector("ul").innerHTML = arr.join("");
    }
}); 
```

