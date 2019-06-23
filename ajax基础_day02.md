# AJAX简要札记

---



## 1.0 jQuery中的ajax

​	如果对于上一次的封装还有一点点印象，那么对于jQuery中的ajax就能很快熟悉

​	只是关于jQuery中的ajax，功能更多，使用更加高效和频繁

#### 关于使用jQuery中的ajax

​	如果要使用自己封装的函数，那么需要引入自己封装的ajaxTools.js文件

```javascript
$.ajax({
    type: 'post',
    url: './server/nav-json.php',
    data: {},
    success: function (result){
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
});

    
```



`有意思的是，如果将自己封装的文件，替换成jquery-1.12.2.min.js文件，发现没任何不妥`

​	也就是说，jquery的使用方式跟上面是差不多的，只是jquery中，还可以在内部有更多的参数和功能，而如果在自己封装的文件上，加入再多的功能，对于难度上来讲是一个挑战，对于实际应用上来说没有必要



| $.ajax({这里传入一个字面量对象})               | 参数说明                         |
| ----------------------------------- | ---------------------------- |
| __url__                             | 接口地址                         |
| __type__                            | 请求方式                         |
| timeout                             | 请求超时，单位是毫秒                   |
| __dataType__                        | 服务器返回的格式, json / xml / jsonp |
| __data__                            | 发送请求的数据                      |
| beforeSend: fucntion() { ...code }  | 请求发起前的调用                     |
| __success: fucntion() { ...code }__ | 成功响应后的调用                     |
| __error: fucntion() { ...code }__   | 错误响应时的调用，e参数为报错信息            |
| complete: fucntion() { ...code }    | 响应完成时的调用                     |



##### 完整的jQuery调用

```javascript
$.ajax({
    type: 'post',
    url: './server/nav-json.php',
    data: {}, //请求需要传递的参数
    // 设置请求超时:单位为毫秒，如果服务器的响应时间超过指定的时候，请求失败
    timeout: 3000,
    dataType:'json', // 设置响应数据的格式  xml json text html script jsonp
    // 发送请求之前的回调
    beforeSend:function(){
        // 在这个回调函数中，如果return false,那么本次请求会中止
        // return false;
    },
    success: function() {
        //请求成功之后的回调
    }, 
    // 请求失败之后的回调
    error:function(e){
        if(e.statusText == "timeout"){
        	alert("请求超时，请重试");
        }
    },
    // 无论请求是成功还是失败都会执行的回调
    complete:function(){
    	console.log('实现一些全局成员的释放，或者页面状态的重置....');
    }
});
```



#### jQuery中ajax的其他用法



##### $.get()的使用

​	本质上只能发送get请求

| $.get(url, data, success, datatype) | 说明        |
| ----------------------------------- | --------- |
| url                                 | 所请求的url   |
| data                                | 请求所传递的数据  |
| success: function() { ...code }     | 成功之后的回调   |
| datatype:                           | 需要返回的数据类型 |



```javascript
$.get("./server/nav-json.php", function() {
  	// 成功回调之后的函数
}, "json")
```



##### $.post()的使用

​	参数一致，用法一直，目的一致

​	代码略.



![jquery-ajax](images\jquery-ajax.jpg)





---



## 3.0 注册案例

​	跟着练习思考使用ajax解决问题的思路

#### 案例的基本情况

![register](images\register.jpg)



##### 准备1：获取验证码的getCode.php文件

```php
<?php
    $arr = array('12345','23456','34567','45678');
    /*生成一个随机索引  array_rand:可以生成指定的数组长度内的索引*/
    $index = array_rand($arr);
    sleep(2);
    echo $arr[$index];
?>
```



##### 准备2：数据的data.json文件

```json
[
  {"name":"jack","pass":"rose","mobile":"12345678901"},	
  {"name":"rose","pass":"123","mobile":"12345678902"},
  {"name":"tom","pass":"123","mobile":"12345678909"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"},
  {"name":"lili","pass":"123","mobile":"1234"}
]
```



##### 准备3：验证用户名是否存在的validataUsername.php文件

```php
<?php
    if($_SERVER['REQUEST_METHOD'] == 'GET'){
        /*1.读取文件*/
        $dataStr = file_get_contents('data.json');
        /*2.转换为数组，因为我们需要判断数组中的成员的name属性是否是指定的用户名--遍历*/
        $dataArr = json_decode($dataStr);
        /*3.遍历*/
        for($i=0;$i<count($dataArr);$i++){
            if($dataArr[$i] -> name == $_GET['name']){
                $arr = array(
                    'code'=>0, //状态码
                    'msg'=>'用户名已存在' //状态信息
                );
                echo json_encode($arr);
                return;
            }
        }
    }
?>
```



##### 准备4：收集用户数据，实现上传和写入

```php
<?php
    $name = $_POST['name'];
    $pass = $_POST['pass'];
    $mobile = $_POST['mobile'];
    /*创建需要进行存储的当前用户注册对象*/
    $obj = array(
        'name' => $name,
        'pass' => $pass,
        "mobile" => $mobile
    );
    /*php无法直接将一个数据存储到文件，它需要先将数据写入到数组，再将数组写入到文件*/
    $arr = file_get_contents('data.json'); //字符串
    $dataArr = json_decode($arr); //将json格式字符串转换为php数组
    /*向数组中添加数据*/
    $dataArr[] = $obj;
    /*将数据写入到文件，写入到文件的数据只能是字符串*/
    $resultStr = json_encode($dataArr); //将数组转换为json格式字符串
    file_put_contents('data.json',$resultStr);
    
    echo json_encode(array('code'=>1,"msg"=>'注册成功'));
?>
```



##### 准备5：在前端页面引入jQuery文件

​	引入jQuery文件，创建开始任务的js标签

```html
<script src="jquery-1.12.2.min.js" ></script>
<script>
  	// 1.0 获取验证码
  	// 2.0 完成验证操作
  	// 3.0 提交表单，进行上传操作
</script>	
```





#### 实现获取验证码

​	步骤1： 为按钮添加单击事件

​	步骤2：收集手机号，向服务器发送获取验证码的请求

​	步骤3：接收验证码，给出响应的提示信息

```javascript
 $(".verify").on("click", function() {
   	// 判断当前的请求是否正在进行
   	if($(this).hasClass("disabled")) {
      	return;
   	}
   	var phone = $(".mobile").val();
   	// 发送请求
   	$.ajax({
      	type: "get",
      	url: "./getCode.php",
      	data: {"phone": phone},
      	beforeSend: function() {
          	var reg = /^1\d{10}$/;
          	// 在请求发送之前，进行验证手机号是否合法
          	if(!reg.test(phone)) {
              	// 元素慢慢出来，提示完之后，再慢慢消失
            	$(".tips > p").fadeIn().delay(2000).fadeOut().text("请输入正确的手机号码!")
          	}
          	// 验证成功 则不可以再次点击
          	$(".verify").addClass("disabled");
          	var total = 5;
          	var timerId = setInterval(function() {
            	total--;
              	$(".verify").val(total + "s之后重新获取")
                if(total <= 0) {
                  	$(".verify").val("重新获取").removeClass("disabled");
                    clearInterval(timerId)
                } 
          	}, 1000)
      	},
      	success: function(res) {
          	alert(res)
      	}
   	})
 })
```



#### 验证用户名是否存在

​	步骤1：添加事件，onblur

​	步骤2：收集用户数据，发送请求

​	步骤3：接收响应，给出提示

```javascript
$(".name").on("blur", function() {
  	var name = $(this).val();
  	$.ajax({
      	type: "get",
      	url: "./server/validataUsername.php",
      	data: {"name":name},
      	dataType: "json", // 接收到的就是一个js的对象，不然就是一个字符串
      	success: function(res) {
          	// console.log(res)
            if(res && res.code == 0) {
              	$(".tips > p").text(res.msg).fadeIn().delay(2000).fadeOut()
            }
      	}
  	})
})
```



####  实现注册 

​	步骤1：添加注册事件

​	步骤2：收集用户数据

​	步骤3：发起ajax请求

​	步骤4：接收响应，给出提示

```javascript
$(".submit").on("click", function() {
  	if($(this).hasClass("disabled")) {
      	return;
  	}
  	// 通过表单序列化的方式来收集用户数据
  	var data = $("#ajaxForm").serialize();
  	$.ajax({
      	type: "post",
      	url: "./server/register.php",
      	data: data,
      	dataType: "json",
        beforeSend: function() {
          	// 用户输入合法的验证
          	// 如果验证通过，开启节流阀，避免重复提交
          	$(".submit").addClass("disabled").val("正在注册中");
      	},
      	success: function(res) {
          	if(res && res.code == 1) {
              	$(".tips > p").text(res.msg).fadeIn().delay(2000).fadeOut()
          	}
      	},
      	error: function() {
          	$(".tips > p").text("注册失败").fadeIn().delay(2000).fadeOut()
      	}, // 不管成功还是失败都会执行
      	complete: function() {
          	$(".submit").removeClass("disabled").val("注册");
      	}
  	})
})
```



在php文件最后加一句

```php
echo json_encode(array('code'=>1, 'msg'=>'注册成功'));
```



| jquery的表单序列化方法                           |      |
| ---------------------------------------- | ---- |
| $("#ajaxForm").serialize();              |      |
| 可以将表单中所有name属性的表单元素的值收集，生成key=value&key=value |      |
| 在ajax中，支持两种格式的参数(1，对象，2，参数格式字符串)         |      |



----



## 4.0 art-template模板引擎



#### 音乐案例

​	渲染数据是开发中常见的活儿

![music-list](images\music-list.jpg)

##### 准备工作

​	准备静态页面，准备json

```json
[
    {
        "id": 1,
        "title": "平凡之路",
        "singer": "朴树",
        "album": "平凡的生命",
        "src": ".\/mp3\/zhangsan.mp3"
    },
    {
        "id": 2,
        "title": "为了遇见你",
        "singer": "薜之谦",
        "album": "为了遇见你",
        "src": ".\/mp3\/See You Again.mp3"
    },
    {
        "id": 3,
        "title": "故乡",
        "singer": "许巍",
        "album": "许巍-作品全集",
        "src": ".\/mp3\/See You Again.mp3"
    }
]
```



##### 步骤1：新建music.php

```php
<?php
  	// 服务器读取json文件
  	echo file_get_contents("music.json");
?>
```



##### 步骤2：前端功能

```html
<script src="jquery-1.12.2.min.js"></script>
<script>
	// 发送ajax请求
  	$.ajax({
		url: "./music.php",
		dataType: 'json',
		success: function (result) {
            var html = "";
            // 生成动态页面结构
            for (var i = 0; i < result.length; i++) {
                html += '<tr>';
                html += '<td>'+result[i].title+'</td>';
                html += '<td>'+result[i].singer+'</td>';
                html += '<td>'+result[i].album+'</td>';
                html += '<td><audio src="'+result[i].src+'" controls></audio></td>';
                html += '<td>';
                html += '<a href="./edit.php?id='+result[i].id+'" class="btn btn-primary">编辑</a>';
                html += '<a href="./delete.php?id='+result[i].id+'" class="btn btn-danger">删除</a>';
                html += '</td>';
                html += '</tr>';
            }
            $("tbody").html(html);
		}
	});
</script>	
```





#### 模板引擎介绍

​	有了模板引擎，再也不用那么复杂的去拼接字符串了

[art-template模板]: https://aui.github.io/art-template/zh-cn/index.html

​	![art-template](images\art-template.jpg)



作用： 为了动态渲染的时候，简化拼接字符串

![template](images\template.jpg)



关键词语：`创建模板标签`、`循环遍历`、`<% 逻辑语句 %>`、`<%= 输出语句 %>`







---



## 5.0 模板引擎的2种语法



#### 原生语法



##### 步骤1：引入文件

```html
<script src="./js/template-native.js"></script>
```



##### 步骤2：创建模板数据

```html
<script type="text/template" id="navTemp">
	<li>
  		<a href="#">
  			<img src="<%= src %>" alt="">
  			<p><%= text %></p>
  		</a>
  	</li>
</script>
```



##### 步骤3：调用模板引擎

```html
<script>
	var obj = {
      	"src": "./images/nav-1.png",
      	"text": "京东超市"
	}
    // 调用函数  templete(模板id, 数据(对象))  返回替换后的DOM结构
    var html = template(navTemp, obj);
  	document.querySelector("ul").innerHTML = html;
</script>
```





![art-template](images\art-template.png)





#### 多个数据的数组动态生成



##### 步骤1： 模板标签

```html
<!-- 这里的item指的是将来传进来的对象的属性 -->
<script type="text/template" id="navTemp">
	<% for(var i = 0; i < items.length; i++) {  %>
        <li>
            <a href="#">
                <img src="<%= items[i].src %>" alt="">
                <p><%= items[i].text %></p>
            </a>
        </li>
    <% } %>
</script>
```

`如果是数组，那么在传入的时候需要包装为一个对象`



##### 步骤2：调用模板

```html
<script>
	var arr = [
    	{
            "src": "./images/nav-1.png",
            "text": "京东超市"
        },
      	{
            "src": "./images/nav-2.png",
            "text": "全球购物"
        },
      	{
            "src": "./images/nav-3.png",
            "text": "京东市场"
        }
	]
    // ！！！！！  把数组转换为一个对象的方式
    var html = template(navTemp, {"items": arr});
  	document.querySelector("ul").innerHTML = html;
</script>
```



#### 标准语法



##### 步骤1：引入插件

```html
<script type="text/template" id="musicTemp">
	{{ each items as value index }}
	<tr>
		<td>{{ items[index].title }}</td>
		<td>{{ value.singer }}</td>
		<td>{{ value.album }}</td>
		<td>
  			<audio src="{{ value.src }}" controls></audio>
  		</td>
  		<td>
  			<a href="./edit.php?id={{ value.id }}" class="btn btn-primary">编辑</a>
  			<a href="./edit.php?id={{ value.id }}" class="btn btn-danger">删除</a>
  		</td>
	</tr>
	{{ /each }}
</script>
```



##### 步骤2：发送ajax请求

```javascript
$.ajax({
  	url: "./music.php",
  	dataType: "json",
  	success: function(res) {
      	var html = temlpate("musicTemp", {"items": res});
      	$("tbody").html(html);
  	}
})
```



![template-new](images\template-new.jpg)





如果参数是对象，就直接传入对象，

如果参数是数组，就包装成数组



![yufa](images\yufa.jpg)



















