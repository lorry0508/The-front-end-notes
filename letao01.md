# 项目介绍

电商全端，包含移动端和PC端的网上商城项目。

移动端 ：包含主流电商业务逻辑，商品展示、商品分类、商品搜索、用户注册、用户登录、收获地址管理 ...

PC端：商城后台管理页面，实现对商品、用户的管理 （增 删 改 查）

## 功能介绍

|     平台     |   模块   |             功能             |
| :----------: | :------: | :--------------------------: |
| 移动端web端  |   首页   |       静态展示页面模块       |
| 移动端web端  |   分类   |      一级分类、二级分类      |
| 移动端web端  |   商品   | 搜索中心、商品列表、商品详情 |
| 移动端web端  |  购物车  |          购物车管理          |
| 移动端web端  |   用户   |     登录、注册、修改密码     |
| 移动端web端  | 收货地址 |    展示、添加、编辑、删除    |
|      -       |    -     |              -               |
| pc端后台管理 |   登录   |          管理员登录          |
| pc端后台管理 | 用户管理 |         用户权限管理         |
| pc端后台管理 | 分类管理 |    一级分类、二级分类管理    |
| pc端后台管理 | 商品管理 |  商品录入、删除、修改、展示  |

## 开发模式

前后端分离

## 技术栈



| 系统分层 | 使用技术                                                |
| -------: | :------------------------------------------------------ |
| 数据层： | MYSQL（wamp环境只会用到数mysql，phpstudy只用开启mysql） |
| 服务层： | NodeJs(express)                                         |
|   前端： | mui font-awesome zepto art-template                     |

# 创建并启动项目

- 开启mysql数据库
- 在项目的根目录下 开启命令行窗口 输入 npm start
- localhost:3000/项目文件夹
- 前台登录 http://localhost:3000/mobile/index.html
  - 用户名 itcast
  - 密码 111111
- 后台登录 http://localhost:3000/admin/login.html
  - 用户名 root
  
  - 密码 123456
  
    

知识点：

- 静态托管
- 后端服务的启动方式
- npm i mqsql安装的不是Mysql而是node操作mysql的工具

# 开发首页

## 引入MUI

#### Mui介绍

- bootstrap 也是一个ui框架  响应式的ui框架  兼容不同终端 可以适配pc端 也可以适配  移动端
- Mui 是一个ui框架 针对移动端开发的ui框架    只能适配移动端（流式布局）
- 学习官网 http://dev.dcloud.net.cn/mui/
- 官方文档 http://dev.dcloud.net.cn/mui/ui/
- 组件展示 http://dcloud.io/hellomui/

```html
<head>
	<meta charset="UTF-8">
	<title>Document</title>
	<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
	<link rel="stylesheet" href="assets/mui/css/mui.min.css">
	<script src="assets/mui/js/mui.min.js"></script>
</head>
```

注意：

- 由于文档中没有明确的头部和底部的html示例，项目中的template.html已经保存的各个页面的基本骨架

## 公共样式

base.css

知识点

- 不要直接用组件的类名来复写css

## 头部

```css
.my-header {
 background: #006699;
}

.my-header h1 {
 color: #FFF;
}

.my-header .mui-icon-search {
 color: #FFF;
}
```

## 底部

```css
.my-footer {
 background: #006699;
}

.my-footer .mui-tab-item {
 color: #FFF;
}

.my-footer .mui-tab-item.mui-active {
 color: darkorange;
}
```

因为购物车图标在mui里没有，所以使用一个更全面的字体图标库 font-awesome

```html
<head>
	<link rel="stylesheet" href="assets/fontAwesome/css/font-awesome.min.css">
</head>
<a class="mui-tab-item" href="cart.html">
 <span class="mui-icon fa fa-shopping-cart"></span>
 <span class="mui-tab-label">购物车</span>
</a>
```

## 轮播图

知识点：

- mui gallery组件

```html
<!-- 轮播图 -->
		<div class="mui-slider">
			<div class="mui-slider-group">
				<div class="mui-slider-item">
					<a href="#">
						<img src="images/banner1.png" />
					</a>
				</div>
				<div class="mui-slider-item">
					<a href="#">
						<img src="images/banner2.png" />
					</a>
				</div>
				<div class="mui-slider-item">
					<a href="#">
						<img src="images/banner3.png" />
					</a>
				</div>
				<div class="mui-slider-item">
					<a href="#">
						<img src="images/banner4.png" />
					</a>
				</div>
				<div class="mui-slider-item">
					<a href="#">
						<img src="images/banner5.png" />
					</a>
				</div>
				<div class="mui-slider-item">
					<a href="#">
						<img src="images/banner6.png" />
					</a>
				</div>
			</div>
			<div class="mui-slider-indicator">
				<div class="mui-indicator mui-active"></div>
				<div class="mui-indicator"></div>
				<div class="mui-indicator"></div>
				<div class="mui-indicator"></div>
				<div class="mui-indicator"></div>
				<div class="mui-indicator"></div>
			</div>
		</div>
<!-- /轮播图 -->
```



## 导航

知识点：

- 行级元素浮动后也可以设置宽度
- mui-clearfix清除浮动
- img后的回车会产生间隙，用veritical-align top 或 display block解决
- img,input,textarea称为替换行级元素，可以用css设置宽度是有效的

```html
<!-- 导航链接 -->
		<div class="nav-links mui-clearfix">
			<a href="#">
				<img src="images/nav1.png">
			</a>
			<a href="#">
				<img src="images/nav2.png">
			</a>
			<a href="#">
				<img src="images/nav3.png">
			</a>
			<a href="#">
				<img src="images/nav4.png">
			</a>
			<a href="#">
				<img src="images/nav5.png">
			</a>
			<a href="#">
				<img src="images/nav6.png">
			</a>
		</div>
<!-- /导航链接 -->
```



## 品牌专区

知识点：

- ul>li*8>a>img[src="image/brand$.png"]

  

```html
<!-- 品牌专区 -->
<div class="brand">
 <img src="images/title0.png" class="title">
 <ul>
  <li><a href=""><img src="images/brand1.png" alt=""></a></li>
  <li><a href=""><img src="images/brand2.png" alt=""></a></li>
  <li><a href=""><img src="images/brand3.png" alt=""></a></li>
  <li><a href=""><img src="images/brand4.png" alt=""></a></li>
  <li><a href=""><img src="images/brand5.png" alt=""></a></li>
  <li><a href=""><img src="images/brand6.png" alt=""></a></li>
  <li><a href=""><img src="images/brand7.png" alt=""></a></li>
  <li><a href=""><img src="images/brand8.png" alt=""></a></li>
 </ul>
</div>
<!-- /品牌专区 -->
```



## 产品布局

知识点：

- box-shadow
- text-decoration: line-through

```html
<!-- 运动生活专区 -->
		<div class="product">
			<img src="images/title1.png" class="title">
			<ul class="mui-clearfix">
				<li>
					<a href="#">
						<img src="images/product.jpg">
						<span class="name">
							adidas阿迪达斯 男式 场下休闲篮球鞋S83700
						</span>
						<p>
							<span>¥560</span>
							<span>¥888</span>
						</p>
						<span class="buy-now">立即购买</span>
					</a>
				</li>
				<li>
					<a href="#">
						<img src="images/product.jpg">
						<span class="name">
							adidas阿迪达斯 男式 场下休闲篮球鞋S83700
						</span>
						<p>
							<span>¥560</span>
							<span>¥888</span>
						</p>
						<span class="buy-now">立即购买</span>
					</a>
				</li>
				<li>
					<a href="#">
						<img src="images/product.jpg">
						<span class="name">
							adidas阿迪达斯 男式 场下休闲篮球鞋S83700
						</span>
						<p>
							<span>¥560</span>
							<span>¥888</span>
						</p>
						<span class="buy-now">立即购买</span>
					</a>
				</li>
				<li>
					<a href="#">
						<img src="images/product.jpg">
						<span class="name">
							adidas阿迪达斯 男式 场下休闲篮球鞋S83700
						</span>
						<p>
							<span>¥560</span>
							<span>¥888</span>
						</p>
						<span class="buy-now">立即购买</span>
					</a>
				</li>
			</ul>
		</div>
<!-- /运动生活专区 -->
```

思考：

- 文字从哪里开始从哪里结束，行高够不够？

# 总结

1. MUI的使用
2. 字体图标库 font-awesome
3. 样式的覆盖
4. 轮播图引用
5. css实战