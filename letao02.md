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
| 移动端web端 |  用户  |   登录、注册、账户管理   |
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



# 开发分类页

## 总体布局

```html
<div class="my-content">
	<div class="category-left">
			
	</div>
	<div class="category-right">
			
	</div>
</div>
```

知识点：

1. 左边固定宽度，右边自适应。浮动元素不会占用它后面兄弟元素的布局空间


## 滚动效果

```html
<div class="my-content">
		<div class="category-left">
			<div class="mui-scroll-wrapper">
				<div class="mui-scroll">
					<!-- 滚动区域的内容 -->
				</div>
			</div>
		</div>
		<div class="category-right">
			<div class="mui-scroll-wrapper">
				<div class="mui-scroll">
					<!-- 滚动区域的内容 -->
				</div>
			</div>
		</div>
</div>
<script>
    mui('.mui-scroll-wrapper').scroll({deceleration: '0.0005'})
</script>
```

知识点：

1. 调整页面滚动条 position:absolute 的定位基准
2. zepto同样可以使用$(function(){}) 来在dom加载好之后执行代码



## 左侧布局

```css
.category-left .links {
	background: #FFF;
}

.category-left .links a {
	display: block;
	height: 45px;
	line-height: 45px;
	text-align: center;
	font-size: 14px;
	border-right: 1px solid #e0e0e0;
	border-bottom: 1px solid #e0e0e0;
}

.category-left .links .active {
	background: #f5f5f5;
	border-right: 1px solid transparent;
}
```



## 右侧布局

```css
.category-right .brand-list {
	margin-top: 10px;
	background: #FFF;
}

.category-right .brand-list li {
	width: 33.333%;
	float: left;
}

.category-right .brand-list li a{
	display: block;
}

.category-right .brand-list li img{
	width: 100%;
}

.category-right .brand-list li p{
	text-align: center;
}
```



## 一级分类数据获取

- 将数据导入数据库

- 注意数据库的连接密码在代码里不要填错


```javascript
// 获取一级分类数据
	$.ajax({
		url: '/category/queryTopCategory',
		type: 'get',
		success: function(response){
            console.log(response)
		}	
	});
```



## 一级分类数据渲染

```javascript
// 获取一级分类数据
	$.ajax({
		url: '/category/queryTopCategory',
		type: 'get',
		success: function(response){
			
			// 所谓模板引擎 作用就是用来帮我们将数据和html拼接好 将拼接好的结果 返回给我们

			// 将数据和html做拼接
			// 1) html模板ID
			// 2) 数据
			// 3) 告诉模板引擎 html模板和数据怎样进行拼接
			var html = template('category-first', {result: response.rows});
			$('#links').html(html);

		}
	});
```

```html
<script id="category-first" type="text/template">
		<% for(var i=0;i<result.length;i++){ %>
			<a
				href="#"
				data-id="<%=result[i].id %>"
				><%=result[i].categoryName %></a>
		<% } %>
</script>
```



## 二级分类数据获取

```javascript
// 1.一级分类添加点击事件
	$('#links').on('click', 'a', function(){
		
		// 2.获取当前点击的一级分类的ID
		var id = $(this).attr('data-id');

		// 3.调用接口 获取数据

	});
```



## 二级分类数据渲染

```javascript
$.ajax({
   url: '/category/querySecondCategory',
   type: 'get',
   data: {
      id: id
   },
   success: function(response){
  
       var html = template('category-second', response);

       $('.brand-list').html(html);
   }
});
```

知识点：

1. 相同逻辑封装成函数，逻辑复用


# 开发搜索页

## 搜索页样式（1）

```css
.search {
	width: 100%;
	height: 30px;
	border: 1px solid #006699;
	position: relative;
	margin-bottom: 15px;
}

.search input[type="text"] {
	width: 100%;
	height: 30px;
	padding-right: 70px;
	border: none;
	background: none;
	margin: 0;
	font-size: 14px;
	color: #474747;
}

.search input[type="button"] {
	width: 60px;
	height: 28px;  //思考
	background: #006699;
	color: #FFF;
	border: none;
	position: absolute;
	right: 0;
	top: 0;
	border-radius: 0;
}
```

思考：为什么按钮高度30px时会多出来一部分

提示：box-sizing border position:abosulte



## 搜索页样式  (2)

```css
.history-top {
	color: #8f8f94;
	font-size: 14px;
	padding-bottom: 15px;
}

.history-top .title {

}

.history-top .clear {
	font-size: 14px;
	line-height: normal;
}

.history-top .mui-icon-trash:before {
	margin-right: 4px;
}
```

问题： line-height normal和1有什么区别

## 搜索结果页布局

将首页产品列表复制过来

```css
.filter {
	width: 92%;
	margin: 10px auto 0 auto;
}

.filter .mui-col-xs-6 {
	height: 35px;
	line-height: 35px;
	font-size: 14px;
	text-align: center;
	background: #efeff4;
}

.filter .mui-icon{
	font-size: 12px;
}
```



## 实现搜索跳转

```javascript
/*
		实现用户点击搜索按钮跳转到搜索结果页
			
			1.给搜索按钮添加点击事件
			2.获取用户输入的搜索关键字
			3.判断用户是否输入了搜索关键字
			4.如果用户没有输入 阻止跳转 并且给出提示
			5.如果用户输入了 跳转到搜索结果页面 并且要将用户输入的关键字带到这个页面去

	*/
```



## 实现搜索历史

知识点：

- mui文档 list组件

```javascript
/*
		实现历史关键字存储

			1.准备一个存储关键字的数组
			2.当用户点击搜索按钮的时候 将用户输入的关键字追加到数组中
			3.将数组存储在本地存储中
			4.在页面一上来的时候 判断本地存储中是否有已经存储的关键字
			5.将数据和HTML拼接 将数据展示在页面中

	*/

```

模板

```HTML
<script id="historyTpl" type="text/template">
		<% for(var i=0;i<result.length;i++){ %>
			<li class="mui-table-view-cell"><%=result[i] %></li>
		<% } %>
	</script>
```

思考：

- 已经搜过的还需要追加吗？

# 总结

1. MUI左右分别滚动的布局
2. 数据库导入数据
3. art-template模板的填充
4. localstorage实现搜索历史的展现