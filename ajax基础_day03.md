# AJAX简要札记

---



## 1.0 跨域请求资源

​	JavaScript出于安全方面的考虑，不允许跨域调用其他页面的对象。那什么是跨域呢，简单地理解就是因为JavaScript同源策略的限制，a.com域名下的js无法操作b.com或是c.a.com域名下的对象。
当协议、子域名、主域名、端口号中任意一个不相同时，都算作不同域。不同域之间相互请求资源，就算作“跨域”。

​	![kuayu](images\kuayu.png)



​	**注意**：__跨域并不是请求发不出去，请求能发出去，服务端能收到请求并正常返回结果，只是结果被浏览器拦截了__。之所以会跨域，是因为受到了同源策略的限制，同源策略要求源相同才能正常进行通信，即协议、域名、端口号都完全一致



| 说明                                       |
| ---------------------------------------- |
| 第一：如果是协议和端口造成的跨域问题“前端”是无能为力的             |
| 第二：在跨域问题上，域仅仅是通过“URL的首部”来识别而不会根据域名对应的IP地址是否相同来判断。“URL的首部”可以理解为“协议, 域名和端口必须匹配” |



#### 所有跨域都必须经信息提供方允许, 如果未经允许即可获取, 那是浏览器同源策略出现漏洞



#### 实现跨域

​	这种实现方式较少设置，一来服务器端不归前端处理，二来这种限制浏览器版本

##### 服务器端设置CORS跨域 

```php
<?php
	// 设置跨域请求
	header("Access-Control-Allow-Origin:*"); // * 允许代表所有域来请求
	header("Access-Control-Allow-Origin:http://day09.com");

	echo file_get_contens("nav.json");
?>
```





---



## 2.0 jsonp的实现原理

​	开发中常用jQuery的jsonp方式来解决跨域问题

#### 通过jQuery实现jsonp的调用

​	步骤其实很简单，核心的代码没几句

##### 步骤1： 发送请求的时候设置dataType类型

```javascript
$.ajax({
  	type: "get",
  	url: "http://day8.com/getnav.php",
  	dataType: "jsonp",
  	success: function(res) {
      	var html = template("navTemp", {"items": res})
        document.querySelector("ul").innerHTML = html
  	}
})
```



##### 步骤2：查看jsonp请求

​	![jsonp](images\jsonp.jpg)

​		拿到了数据，但是由于没有调用，所以渲染不成功



##### 步骤3：服务器端的配合

```php
<?php
  	// 这就是客户端请求时传递过来的函数名称
  	$callback = $_GET["callback"];
	// 读取数据
	$data = file_get_contens("nav.json");
	// 返回调用函数的形式，只不过要传递前端需要的数据
	echo $callback."('.$data.')"; // acb($data)
?>
```





#### jsonp实现原理

​	页面中有几个标签允许跨域请求资源  `a链接的href` 、 `img的src` 、`link的href` 、`script的src`



##### 在php文件中模拟返回数据

```php
<?php
  	// echo "abc";  去往js后由于未定义会报错
  	// echo "alert(123)";  // 以下的形式，符合js的语法
	
	$callback = $_GET["callback"];
	echo $callback."()";
?>
```



##### 在前端接收数据

```html
<script>
	function test() {
      	console.log(123)
	}
</script>
<!-- 通过本能的跨域执行请求，后面拼接一个回调函数 -->
<script src="corss.php?callback=test"></script>
```





#### 完整实现

​	通过前后端整体实现

##### 后端进行拼接

```php
<?php
	$callback = $_GET["callback"];
	$arr = array(
    	"name" => "jack",
      	"age" => 20
    );
	// $data = json_encode($arr);

	$data = file_get_contents("nav.json");
	// 返回的数据最终只能是字符串
	echo $callback."(".$data.")";
?>
```



##### 前端进行请求和函数的调用

 ```html
<script>
  	// 接收数据
	function test(data) {
      	console.log(123)
	}
</script>

<!-- 通过本能的跨域执行请求，后面拼接一个回调函数 -->
<script src="corss.php?callback=test"></script>
 ```

![jsonp-in](images\jsonp-in.jpg)



---



## 3.0 jsonp的案例

​	以后的工作模式中会大量使用到jsonp跨域请求资源

#### 接口化开发

​	在近几年的网站开发中，越来越成熟的表现出前后端分离，也就是说前端开发人员需要了解后端的需求，但并不需要操作后端的逻辑，因为后端有其自己的框架和一套熟练的规范

​	而后端做好了个各种的数据搭建之后，会针对性的给出一些接口通常叫做接口文档，里面会详细的描述了该请求的方式，请求的地址，所传的参数，参数的重要性 ...

​	所以前端大部分做的事件，就是根据写好的开发文档，按需接收数据，然后通过模板技术动态生成页面

##### 接口文档

​	![jiekou-md](images\jiekou-md.jpg)

​	

##### 开放的api接口

​	在线上也有一些开放的接口文档可以被拿来当前的网页使用，在使用的时候，也需要按照文档的提示

![map-api](images\map-api.jpg)





#### 获取天气案例

​	使用jsonp方式获取其他平台提供的信息

##### 引入文件，调用方法

```html
<script src="./js/jquery.min.js"></script>
<script src="./js/template-native.js"></script>
<script>
	$.ajax({
		type:'post',
        url:"http://api.map.baidu.com/telematics/v3/weather",
        data:{
        	"ak":"zVo5SStav7IUiVON0kuCogecm87lonOj",
        	"location":"北京",
        	"output":'json'
        },
        dataType:'jsonp',
        // jsonpCallback:function(){}
        success:function(result){
        	console.log(result);
        	var html = template("weatherTemp",{"items":result.results[0].weather_data});
        	document.querySelector("tbody").innerHTML = html;
        }
	});
</script>
```



##### 创建模板，渲染数据

```html
    <script type="text/template" id="weatherTemp">
        <% for(var i=0;i<items.length;i++){ %>
            <tr>
                <td><%=items[i].date%></td>
                <td><img src="<%=items[i].dayPictureUrl%>" alt=""></td>
                <td><img src="<%=items[i].nightPictureUrl%>" alt=""></td>
                <td><%=items[i].temperature%></td>
                <td><%=items[i].weather%></td>
                <td><%=items[i].wind%></td>
            </tr>
        <% }%>
    </script>
```



---



## 4.0 XMLHttpRequest2.0-formdata



#### timeout

​	timeout代表延迟，可以当作属性设置延迟，单位为毫秒，也可以作为事件

```javascript
document.querySelector("button").onclick = function(){
	var xhr = new XMLHttpRequest();

    // 设置请求行
    xhr.open("get","01-timeout.php");
    // 设置请求头:get不需要设置
    // 设置请求体
    xhr.send(null);

    // 设置超时
    xhr.timeout = 2000;
    xhr.ontimeout = function(e){
       console.log(e);
    }

    // 接收响应
    xhr.onreadystatechange = function(){
         if(xhr.status == 200 && xhr.readyState == 4){
              alert(xhr.responseText);
          }
    }
}
```



#### FromData(form表单)



##### 正常的post请求

​	form内表单元素的name属性是必须的，但是不需要再一个个获取了

​	注意使用的方式和细节

```html
<script>
        document.querySelector("#sub").onclick = function(){
            var xhr = new XMLHttpRequest();

            xhr.open("post","02-formData.php");
            // 1.手动拼接
            // 2.如果是jq,那么就可以使用表单序列化方法
            // 3.现在在XMLHttpRequest2.0   ,我们可以使用FormData来收集表单数据

            // 1.获取表单
            var myform = document.querySelector("#form1");
            // 2.将表单做为参数传递，在创建formData对象的时候
            var formdata = new FormData(myform);

            // 特点之一：可以自由的追加参数
            formdata.append("address","传智播客");
            // 3.生成的formData对象就可以直接做为异步对象的参数传递
            xhr.send(formdata);

            xhr.onreadystatechange = function(){
                if(xhr.status == 200 && xhr.readyState == 4){
                    console.log(xhr.responseText);
                }
            }
        }
</script>
```



##### 上传文件的post请求

​	!!!!!    注意，这里设置了请求头的话，数据无法传递

```html
<script>
        document.querySelector("#sub").onclick = function(){
            var xhr = new XMLHttpRequest();
            xhr.open("post","03-uploadFile.php");
            //如果设置了请求头，那么文件数据无法正确的传递
            // xhr.setRequestHeader("Content-Type","application/x-www-form-urlencoded");
            var myform = document.querySelector("#form1");
            var formData = new FormData(myform);
            
            xhr.send(formData);
            xhr.onreadystatechange = function(){
                if(xhr.status == 200 && xhr.readyState == 4){
                    console.log(xhr.responseText);
                }
            }
        }
</script>
```



##### onprogress事件的用法

​	！！！！__这段代码一定要在send方法之前__

```javascript
// 监听文件上传的进度:这个监听必须在send之前来设置
xhr.upload.onprogress = function(e){
	var current = e.loaded;//上传了多少
    var total = e.total;//总共
    var percent = current / total * 100 +"%";
    document.querySelector(".in").style.width = percent;
    document.querySelector("span").innerHTML = Math.floor(current / total * 100)+"%";
}
```



---



## 5.0 学生管理系统

​	初步体验以接口为开发流程的步骤

#### 建立项目

​	打开服务器，打开数据库，找到demo文件夹中的test.sql

##### 连接数据库，执行sql文件

![connect-sql](images\connect-sql.jpg)



##### 查询sql文件中的语句

​	由于源文件有一些问题，所以只需要执行内部的一段语句即可



![sql](images\sql.jpg)



```sql
DROP TABLE IF EXISTS `teacher`;
CREATE TABLE `teacher` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(500) DEFAULT NULL,
  `password` varchar(500) DEFAULT NULL,
  `name` varchar(500) DEFAULT NULL,
  `school` varchar(50) DEFAULT NULL,
  `age` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=MyISAM AUTO_INCREMENT=85 DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of teacher
-- ----------------------------
INSERT INTO `teacher` VALUES ('1', 'jack', '123', '杰克', '传智播客', '19');
INSERT INTO `teacher` VALUES ('2', 'rose', '321', '罗丝', '黑马程序员', '19');
INSERT INTO `teacher` VALUES ('3', 'tom', '123', '汤姆', '传智播客', '19');
INSERT INTO `teacher` VALUES ('4', '张三', '321', '三哥', '黑马程序员', '19');
INSERT INTO `teacher` VALUES ('5', 'mary', '123456', '玛丽', '黑马程序员', '20');
INSERT INTO `teacher` VALUES ('6', 'tark', '123', '坦克', '传智学院', '20');
```



##### 刷新数据库

![refresh](images\refresh.jpg)



![click-info](images\click-info.jpg)



#### 请求数据渲染页面

```html
<script src="../lib/js/jquery-1.12.4.js"></script>
<!--引入模板引擎js文件-->
<script src="../lib/js/template-native.js"></scrip
<script>
	$.ajax({
            type:'get',
            url:'findUsers.php',
            data:{
                pageNum:cpage || 1,
                pageSize:pageSize || 2
            },
            dataType:"json",
            success:function(result){
                console.log(result);
                // 渲染数据列表
                var html = template("stuListTemp",result);
                $("tbody").html(html);
            }
	});
</script>
```



##### 创建数据模板

```html
<!-- 首页动态数据列表的模板 -->
<script type="text/template" id="stuListTemp">
        <% for(var i=0;i<list.length;i++){%>
            <tr>
                <td><%= (pageNum - 1) * pageSize + i + 1 %></td>
                <td><%=list[i].name%></td>
                <td><%=list[i].age%></td>
                <td><%=list[i].username%></td>
                <td><%=list[i].school%></td>
                <td><a href="javascript:;" data-id="<%=list[i].id%>" class="delete" title="删除"><span class="glyphicon glyphicon-remove"></span></a></td>
            </tr>
        <% }%>
</script>
```



##### 封装渲染的请求

```javascript
// 动态展示首页数据
function render(cpage,pageSize){
	$.ajax({
		type:'get',
        url:'findUsers.php',
        data:{
        	pageNum:cpage || 1,
            pageSize:pageSize || 2
        },
        dataType:"json",
        success:function(result){
            console.log(result);
            // 渲染数据列表
            var html = template("stuListTemp",result);
            $("tbody").html(html);
        }
     });
}
render();
```





#### 点击按钮增加数据

```javascript
// 实现新增操作
$("#save").on("click",function(){
	$.ajax({
 		type:'post',
        url:'saveUser.php',
        data:$("#newUser").serialize(),
        dataType:'json',
        beforeSend:function(){},
        success:function(result){
        	console.log(result);
        	if(result.status == "ok"){
            	alert('新增成功');
                render();
            }
         }
     });
});
```





#### 点击按钮删除数据

```javascript
// 实现删除功能
$("tbody").on("click",".delete",function(){
	// 底部用户是否真的需要删除
	if(window.confirm('请问是否真的需要删除?')){
		var id = $(this).attr("data-id");
        $.ajax({
        	type:'post',
            url:'removeUser.php',
            data:{"id":id},
            dataType:'json',
            success:function(result){
            	if(result.status == "ok"){
                	alert('删除成功')
                    render();
                 }
             }
         });
     }
});
```









