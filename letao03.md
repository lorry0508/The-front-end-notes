# 项目介绍

电商全端，包含移动端和PC端的网上商城项目。

移动端 ：包含主流电商业务逻辑，商品展示、商品分类、商品搜索、用户注册、用户登录、收获地址管理 ...

PC端：商城后台管理页面，实现对商品、用户的管理 （增 删 改 查）

## 功能介绍	

|   平台    |  模块  |       功能       |
| :-----: | :--: | :------------: |
| 移动端web端 |  首页  |    静态展示页面模块    |
| 移动端web端 |  分类  |   一级分类、二级分类    |
| 移动端web端 |  商品  | 搜索中心、商品列表、商品详情 |
| 移动端web端 | 购物车  |     购物车管理      |
| 移动端web端 |  用户  |   登录、注册、修改密码   |
| 移动端web端 | 收货地址 |  展示、添加、编辑、删除   |
|    -    |  -   |       -        |
| pc端后台管理 |  登录  |     管理员登录      |
| pc端后台管理 | 用户管理 |     用户权限管理     |
| pc端后台管理 | 分类管理 |  一级分类、二级分类管理   |
| pc端后台管理 | 商品管理 | 商品录入、删除、修改、展示  |

## 开发模式

前后端分离

## 技术栈



| 系统分层 | 使用技术                                     |
| ---: | :--------------------------------------- |
| 数据层： | MYSQL（wamp环境只会用到数mysql，phpstudy只用开启mysql） |
| 服务层： | NodeJs(express)                          |
|  前端： | mui font-awesome zepto art-template      |



# 开发搜索结果页

## 实现搜索参数获取

```javascript
/**
 * 获取地址栏中的参数
 * @param  {string} url 地址字符串
 * @param  {string} name 要获取的参数名称
 * @return {string}     参数名称对应的参数值
 */
function getParamsByUrl(url, name) {
	var params = url.substr(url.indexOf('?')+1);
	var param = params.split('&');
	for(var i=0;i<param.length;i++){
		var current = param[i].split('=');
		if(current[0] == name){
			return current[1]
		}
	}
	return null;
}
```



## 实现搜索结果渲染

1. 发送AJAX请求请求接口/product/queryProduct
2. 获得接口返回的数据并将数据渲染到页面中显示

```javascript
html = template('searchTpl', response);
$('#search-box').html(html);
```



## 实现搜索结果排序

1. 绑定点击按钮事件
2. 点击时更改配置参数
3. 页面重置
4. 请求新的参数



知识点：

- this的指向

```javascript
// 获取地址栏中用户输入的关键字
var keyword = getParamsByUrl(location.href, 'keyword');
// 当前页
var page = 1;
// 页面中的数据
var html = "";

// 价格排序规则 升序
var priceSort = 1;
var num=1;
var This = null;

var order={}
$(function(){
	
	/*
		根据用户输入的关键字获取搜索结果

			1.获取到地址栏中用户输入的搜索关键字
			2.用关键字去调取搜索接口
			3.将搜索结果展示在页面中

	*/

	mui.init({
	  pullRefresh : {
	    container: '#refreshContainer',//待刷新区域标识，querySelector能定位的css选择器均可，比如：id、.class等
	    up : {
	      height:50,//可选.默认50.触发上拉加载拖动距离
	      auto:true,//可选,默认false.自动上拉加载一次
	      contentrefresh : "正在加载...",//可选，正在加载状态时，上拉加载控件上显示的标题内容
	      contentnomore:'没有更多数据了',//可选，请求完毕若没有更多数据时显示的提醒内容；
	      callback: getData //必选，刷新函数，根据具体业务来编写，比如通过ajax从服务器获取新数据；
	    }
	  }
	});

	// callback 页面一上来的时候 会自动调用一次
	// 当页面上拉到底部时 还会继续调用
	
	/*
		按照价格对商品进行排序

			1.对价格按钮添加轻敲事件
			2.将价格排序规则传递到接口中
			3.对之前的各种配置进行初始化
				清空页面中的数据
				恢复当前页的值为1
				重新开启上拉加载
			4.将排序后的结果重新展示在页面中
	*/

	$('#priceSort').on('tap', function(){
		// 更改价格排序条件
		priceSort = priceSort == 1 ? 2 : 1;
		order={price:priceSort}
		// 对之前的各种配置进行初始化
		html = "";
		page = 1;
		mui('#refreshContainer').pullRefresh().refresh(true);
		getData();
	});

	$('#num').on('tap', function(){
		// 更改销量排序条件
		num = num == 1 ? 2 : 1;
		order={num:num}
		console.log(num);
	
		// 对之前的各种配置进行初始化
		html = "";
		page = 1;
		mui('#refreshContainer').pullRefresh().refresh(true);
		getData();
	});
});

/**
 * 获取地址栏中的参数
 * @param  {string} url 地址字符串
 * @param  {string} name 要获取的参数名称
 * @return {string}     参数名称对应的参数值
 */
function getParamsByUrl(url, name) {
	var params = url.substr(url.indexOf('?')+1);
	var param = params.split('&');
	for(var i=0;i<param.length;i++){
		var current = param[i].split('=');
		if(current[0] == name){
			return current[1]
		}
	}
	return null;
}

function getData() {
	if(!This){
    This = this;
  }


  $.ajax({
		url: '/product/queryProduct',
		type: 'get',
		data: {
			page: page++,
			pageSize: 3,
			proName: keyword,
			price: priceSort,
		},
		success: function(response){

			if(response.data.length > 0){
				html += template('searchTpl', response);
				$('#search-box').html(html);
				// 告诉上拉加载组件当前数据加载完毕
				This.endPullupToRefresh(false);
			}else {
				// 告诉上拉加载组件当前数据加载完毕,并且已经没有更多数据
				This.endPullupToRefresh(true);
			}
		}
	});

}



```

思考：

- 代码还能优化吗？造成this无法确定的根本原因是什么？

# 实现页面跳转

知识点：

- mui默认禁止了a标签的跳转，所以要在全局恢复a标签的跳转

```javascript
$(function(){
	// 恢复A元素的跳转
	$('body').on('tap', 'a', function(){
		mui.openWindow({
			url: $(this).attr('href')
		});
	});
});
```

# 开发注册页

## 注册页面布局

知识点：

- mui文档 input组件  类名mui-input-group

```html
<form class="mui-input-group">
		    <div class="mui-input-row">
		        <label>用户名</label>
		    	<input type="text" class="mui-input-clear" placeholder="请输入用户名" name="username">
		    </div>
		    <div class="mui-input-row">
		        <label>手机号</label>
		    	<input type="text" class="mui-input-clear" placeholder="请输入手机号" name="mobile">
		    </div>
		    <div class="mui-input-row">
		        <label>密码</label>
		        <input type="password" class="mui-input-password" placeholder="请输入密码" name="password">
		    </div>
		    <div class="mui-input-row">
		        <label>确认密码</label>
		        <input type="password" class="mui-input-password" placeholder="请确认密码" name="againPass">
		    </div>
		    <div class="mui-input-row">
		        <label>认证码</label>
		        <input type="text" class="mui-input-clear" placeholder="认证码" name="vCode">
		        <a href="javascript:;" class="getCode" id="getCode">获取认证码</a>
		    </div>
		    <div class="mui-button-row">
		        <button type="button" class="mui-btn mui-btn-primary" id="register-btn">注册</button>
		    </div>
		</form>
		<a href="login.html" class="login-now">立即登录</a>
```

## 注册页面功能

知识点

- 获取认证码
- 校验表单
- 提交表单
- mui dialog组件  toast方法提示

```javascript
$('#register-btn').on('click', function(){

		var username = $('[name="username"]').val();
		var mobile = $('[name="mobile"]').val();
		var password = $('[name="password"]').val();
		var againPass = $('[name="againPass"]').val();
		var vCode = $('[name="vCode"]').val();

		if(!username){
			mui.toast("请输入用户名");
			return;
		}

		if(mobile.length < 11){
			mui.toast("请输入合法的手机号");
			return;
		}

		if(password != againPass){
			mui.toast("两次输入的密码不一样");
			return;
		}

		$.ajax({
			url: '/user/register',
			type: 'post',
			data: {
				username: username,
				password: password,
				mobile: mobile,
				vCode: vCode
			},
			success: function(res){
				console.log(res,22222);
				/* alert("注册成功"); */
				if(res.success){
					alert("注册成功");
					setTimeout(function(){
						location.href = "login.html";
					}, 2000)
				}else if(res.error==401){
					mui.toast('验证码错误')
				}else if(res.error==403){
					mui.toast('输入错误')
				}else if(res.error==400){
					mui.toast('未登录')
				}
			}
		})



	});
/**
	 * 获取认证码
	 * 1.给获取认证码按钮添加点击事件
	 * 2.调用接口获取认证码
	 * 3.将认证码输出到控制台
	 */
	
	$('#getCode').on('click', function(){

		$.ajax({
			url: '/user/vCode',
			type: 'get',
			success: function(res){
				console.log(res.vCode);
			}
		})

	});
```

# 开发登录页

知识点：

- jquery/zepto ajax方法中的选项：beforeSend

```javascript
$(function(){

	/**
	 * 用户登录
	 * 1.获取登录按钮并且添加点击事件
	 * 2.获取到用户输入的表单信息
	 * 3.调用登录接口实现登录
	 * 4.如果用户登录成功跳转到会员中心
	 */
	
	$('#login-btn').on('click', function(){

		var username = $.trim($("[name='username']").val());
		var password = $.trim($("[name='password']").val());

		if(!username){
			mui.toast("请输入用户名");
			return;
		}

		if(!password){
			mui.toast("请输入密码");
			return;
		}

		$.ajax({
			url: '/user/login',
			type: 'post',
			data: {
				username: username,
				password: password
			},
			beforeSend: function(){
				$('#login-btn').html("正在登录...");
			},
			success: function(res){

				mui.toast("登录成功");

				$('#login-btn').html("登录");

				setTimeout(function(){
   					location.href = "user.html";
				}, 2000);
			}
		})


	});

});
```

# 开发会员中心页

## 会员中心布局

知识点：

- mui文档 list组件 media list

```html
<div class="mui-content">
		<ul class="mui-table-view" id="userInfoBox">
		    
		</ul>
		<ul class="mui-table-view">
		    <li class="mui-table-view-cell">
		        <a class="mui-navigate-right">修改密码</a>
		    </li>
		    <li class="mui-table-view-cell">
		        <a class="mui-navigate-right">我的购物车</a>
		    </li>
		    <li class="mui-table-view-cell">
		        <a class="mui-navigate-right">收货地址管理</a>
		    </li>
		    <li class="mui-table-view-cell">
		        <a class="mui-text-center" id="logout">退出登录</a>
		    </li>
		</ul>
	</div>
	<script type="text/template" id="userTpl">
		<li class="mui-table-view-cell mui-media">
		    <a href="javascript:;">
		        <img class="mui-media-object mui-pull-left" src="images/user.jpg">
		        <div class="mui-media-body">
		            <%=mobile %>
		            <p class='mui-ellipsis'>账号:<%=username %></p>
		        </div>
		    </a>
		</li>
	</script>
```

## 退出登录功能

```javascript
/**
 * 退出登录
 * 1.获取到退出登录按钮并添加点击事件
 * 2.调用退出登录接口实现 退出登录
 * 3.如果退出成功 跳转到首页
 */

$('#logout').on('click', function(){
 $.ajax({
  url: '/user/logout',
  type: 'get',
  success: function(res){
   if(res.success){
    mui.toast("退出登录成功");
    setTimeout(function(){
     location.href = "index.html";
    },2000)
   }
  }
 })
});
```

## 个人信息请求

知识点：

- 在 ```ajax```方法里传入 ```async: false```,  **同步**的 ```ajax``` 和 ```alert``` 一样，会**阻塞**浏览器的 JS 和 UI 线程，以此来拦住用户，防止未登录的用户也能渲染会员中心部分的html

```javascript
$.ajax({
 url: '/user/queryUserMessage',
 type: 'get',
 // 同步
 async: false,
 success: function(res){
  console.log(res);

  // 用户没有登录
  if(res.error && res.error == 400){

   location.href = "login.html";

  }

  userInfo = res;

 }
});

$(function(){
	/**
	 * 获取用户信息 并且要处理用户未登录的问题
	 */

	// 拼接模板
	var html = template('userTpl', userInfo);

	// 展示用户信息
	$('#userInfoBox').html(html);
});
```

注意：

- 实际开发中需要避免阻塞线程的代码

## 个人信息渲染

```html
<script type="text/template" id="userTpl">
 <li class="mui-table-view-cell mui-media">
     <a href="javascript:;">
         <img class="mui-media-object mui-pull-left" src="images/user.jpg">
         <div class="mui-media-body">
             <%=mobile %>
             <p class='mui-ellipsis'>账号:<%=username %></p>
         </div>
     </a>
 </li>
</script>
```



```javascript
// 拼接模板
var html = template('userTpl', userInfo);

// 展示用户信息
$('#userInfoBox').html(html);
```

# 总结

- mui list组件 dialog组件
- this的指向
- 未登录状态的拦截