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



# 开发修改密码页	

同注册页

```javascript
$(function(){

	/**
	 * 修改密码
	 * 1.获取修改密码按钮并添加点击事件
	 * 2.获取用户输入的信息
	 * 3.对用户输入的信息做校验
	 * 4.调用修改密码接口 实现修改密码功能
	 * 5.跳转到登录页面 重新登录
	 */
	
	$('#modify-btn').on('tap', function(){

		// 原密码
		var originPass = $.trim($("[name='originPass']").val());
		// 新密码
		var newPass = $.trim($("[name='newPass']").val());
		// 确认新密码
		var confirmNewPass = $.trim($("[name='confirmNewPass']").val());
		// 认证码
		var vCode = $.trim($("[name='vCode']").val());

		if(!originPass){

			mui.toast('请输入原密码');

			return;

		}

		if(newPass != confirmNewPass){

			mui.toast('两次输入的密码不一致');

			return;

		}

		// 发送修改密码请求
		$.ajax({
			url: '/user/updatePassword',
			type: 'post',
			data: {
				oldPassword: originPass,
				newPassword: newPass,
				vCode: vCode
			},
			success: function(res){
				if(res.success){

					mui.toast("修改密码成功");

					setTimeout(function(){
						location.href = "login.html";
					},2000)

				}
			
			}
		})



	});

	/**
	 * 获取认证码
	 */
	
	$('#getCode').on('tap', function(){

		$.ajax({
			url: '/user/vCodeForUpdatePassword',
			type: 'get',
			success: function(res){
				// 将认证码显示在控制台中
				console.log(res.vCode);
			}
		})

	});

});
```



# 开发收货地址相关功能

## 收货地址列表页布局

知识点：

- mui list

```html
<div class="mui-content">
 <a href="addAddress.html?isEdit=0" class="addAddress">添加收货地址</a>
 <ul class="mui-table-view" id="address-box">
     
 </ul>
</div>
<script type="text/template" id="addressTpl">
 <% for(var i=0;i<result.length;i++){ %>
 <li class="mui-table-view-cell mui-media">
        <div class="mui-media-body mui-slider-handle">
             邮编：<%=result[i].postCode %> 收货人：<%=result[i].recipients %> 
            <p class='mui-ellipsis'>
             <%=result[i].address %> <%=result[i].addressDetail %>
            </p>
        </div>
 </li>
 <% } %>
</script>
```

## 添加收货地址页布局

## 收货地址列表渲染

## 添加收货地址页逻辑

知识点：

- 使用mui picker组件来做选择地址的功能（文档）
- city.js里面预先保存了全国省市区的数据，需要引入后配合picker使用

```javascript
$(function(){
 // 创建picker选择器
 var picker = new mui.PopPicker({layer:3});

 // 为picker选择器添加数据
 picker.setData(cityData);

 // 当用户敲击文本框的时候
 $('#selectCity').on('tap', function(){

  // 显示picker选择器
  picker.show(function(selectItems){

   // 将用户选择的内容显示在文本框中
   $('#selectCity').val(selectItems[0].text + selectItems[1].text + selectItems[2].text);
  });

 });


 /**
  * 添加收货地址
  * 1.获取收货地址管理按钮并且添加点击事件
  * 2.获取用户输入的表单信息
  * 3.对用户输入的表单信息进行校验
  * 4.调用添加收货地址接口 实现功能
  * 5.跳转回收货地址列表页面
  */

 $('#addAddress').on('tap', function(){

  var username = $.trim($("[name='username']").val());
  var postCode = $.trim($("[name='postCode']").val());
  var city = $.trim($("[name='city']").val());
  var detail = $.trim($("[name='detail']").val());

  if(!username) {
   mui.toast("请输入收货人姓名");
   return;
  }

  if(!postCode) {
   mui.toast("请输入邮政编码");
   return;
  }

  var data = {
   address: city,
   addressDetail: detail,
   recipients: username,
   postcode: postCode
  };

  var url = "/address/addAddress";

  $.ajax({
   url: url,
   type: 'post',
   data: data,
   success: function(res) {
    if(res.success) {
     mui.toast("地址添加成功");
     
     setTimeout(function(){
      location.href = "adress.html";
     },2000)

    }

   }
  })


 });



});
```



## 编辑与删除按钮布局

知识点：

- mui 滑动触发列表菜单
- class="mui-slider-handle"
- 提前把id存起来，等下做删除和编辑操作时作为参数进行传参

```html
<div class="mui-slider-right mui-disabled">
 <span
  class="mui-btn mui-btn-blue edit-btn"
  data-id="<%=result[i].id %>">编辑</span>
 <a
  href="javascript:;"
  class="mui-btn mui-btn-red delete-btn"
  data-id="<%=result[i].id %>">删除</a>
</div>
<div class="mui-media-body mui-slider-handle">
	           	邮编：<%=result[i].postCode %> 收货人：<%=result[i].recipients %> 
	            <p class='mui-ellipsis'>
	            	<%=result[i].address %> <%=result[i].addressDetail %>
	            </p>
</div>
```



## 删除确认框

知识点：

- mui dialog组件 comfirm
- 模板由于一开始没有渲染在dom中，可以使用事件委托来监听模板的事件
- mui.swipeoutClose(要滑回去的元素);

```javascript
/**
 * 删除收货地址
 * 1.给删除按钮添加点击事件
 * 2.弹出一个删除确认框
 * 3.如果用户点击确认 删除
 * 4.调用删除收货地址的接口 完成删除功能
 * 5.刷新当前页面
 */

$('#address-box').on('tap','.delete-btn',function(){

 var id = this.getAttribute('data-id');

 var li = this.parentNode.parentNode;

 mui.confirm("确认要删除吗?",function(message){

  // 确认删除
  if(message.index == 1) {
   $.ajax({
    url: '/address/deleteAddress',
    type: 'post',
    data: {
     id: id
    },
    success: function(res){
     // 删除成功
     if(res.success){
      // 重新加载当前页面
      location.reload();
     }
    }
   })
  }else{
   // 取消删除
   // 关闭列表滑出效果
   mui.swipeoutClose(li);
  }
 });
});
```

## 收货地址编辑的跳转

知识点：

- 页面跳转后，上个页面的js上下文就已经被销毁，所以通过localstorage来传递数据，把要编辑的地址原数据传递过去
- 编辑地址页面和添加地址页可以复用，通过url的参数来区分当前页面的功能
- getParamsByUrl函数需要复用，抽离到public.js
- 表单由于在编辑地址时需要渲染数据，所以相关html需要放在模板里
- 注意跳转时js的跳转与a标签的跳转冲突

```javascript
//address.js
/**
 * 编辑收货地址
 * 1.给编辑按钮添加点击事件
 * 2.跳转到收货地址编辑页面 并且要将编辑的数据传递到这个页面
 * 3.将数据展示在页面中
 * 4.给确定按钮添加点击事件
 * 5.调用接口 执行编辑操作
 * 6.跳转回收货地址列表页面
 */

$('#address-box').on('tap', '.edit-btn', function(){
 var id = this.getAttribute('data-id');
 for(var i=0;i<address.length;i++) {
  if(address[i].id == id) {
   localStorage.setItem('editAddress',JSON.stringify(address[i]));
   // 终止循环
   break;
  }
 }
 // 跳转到编辑页面
 location.href = "addAddress.html?isEdit=1";
});
```

```javascript
//addAddress.js
var isEdit = Number(getParamsByUrl(location.href, 'isEdit'));

if(isEdit){
 // 编辑操作
 if(localStorage.getItem("editAddress")){
  var address = JSON.parse(localStorage.getItem("editAddress"));
  var html = template("editTpl",address);
  $('#editForm').html(html); 
 }
}else{
 // 添加操作
 var html = template("editTpl",{});
 $('#editForm').html(html);
}
```

## 收货地址编辑功能的开发

知识点：

- 根据isEdit参数来决定提交按钮功能的不同

```javascript
$('#addAddress').on('tap', function(){

 var username = $.trim($("[name='username']").val());
 var postCode = $.trim($("[name='postCode']").val());
 var city = $.trim($("[name='city']").val());
 var detail = $.trim($("[name='detail']").val());

 if(!username) {
  mui.toast("请输入收货人姓名");
  return;
 }

 if(!postCode) {
  mui.toast("请输入邮政编码");
  return;
 }

 var data = {
  address: city,
  addressDetail: detail,
  recipients: username,
  postcode: postCode
 };

 if(isEdit){
  // 编辑操作
  var url = "/address/updateAddress";
  data.id = address.id;
 }else {
  // 添加操作
  var url = "/address/addAddress";
 }

 $.ajax({
  url: url,
  type: 'post',
  data: data,
  success: function(res) {
   if(res.success) {
    if(isEdit){
     mui.toast("地址修改成功");
    }else{
     mui.toast("地址添加成功");
    }
    setTimeout(function(){
     location.href = "adress.html";
    },2000)

   }

  }
 })


});
```

# 开发商品详情相关功能

## 商品详情页布局

```html
<div class="mui-content">

  <div id="product-box">

  </div>

  <div class="num">
    <div class="mui-pull-left">数量：</div>
    <div class="mui-pull-left selectNum">
      <span id="reduce">-</span>
      <input type="text" value="1" id="inp">
      <span id="increase">+</span>
    </div>
    <div class="mui-pull-left">剩余：44件</div>
  </div>

  <div class="btns">
    <a href="cart.html">查看购物车</a>
    <span id="addCart">加入购物车</span>
  </div>
</div>
```

## 商品详情页渲染

知识点：

- 轮播图需要在模板渲染后再进行一次初始化

```html
<script type="text/template" id="productTpl">
 
 <div class="mui-slider">
  <div class="mui-slider-group">
   <% for(var i=0;i<pic.length;i++){ %>
   <div class="mui-slider-item">
    <img src="<%=pic[i].picAddr %>"/>
   </div>
   <% } %>
  </div>
 </div>
 
 <div class="product-title">
  <%=proName %>
 </div>
 <div class="price">
  价格：
  <span class="now">¥<%=price%></span>
  <span class="old">¥<%=oldPrice%></span>
 </div>
 <div class="size">
  尺码：
  <% var size = size.split('-') %>
  <!-- ["35","56"] -->
  <% for(var i=size[0];i<=size[1];i++){ %>
  <span><%=i %></span>
  <% } %>
 </div>
</script>
```

```javascript
$.ajax({
		url: '/product/queryProductDetail',
		type: 'get',
		data: {
			id: id
		},
		success: function(res){
			console.log(res);
			// 库存数量
			kucunNum = res.num;
			// 产品ID
			productId = res.id;
			var html = template("productTpl", res);
			$('#product-box').html(html);
			//获得slider插件对象
			var gallery = mui('.mui-slider');
			gallery.slider();

		}
	});
```

## 商品详情页功能

选择尺码和数量

```javascript
$('#product-box').on('tap', '.size span', function(){

 $(this).addClass('active').siblings('span').removeClass('active');

 // 用户选择的尺码
 size = $(this).html();

});

var oInp = $('#inp');
	$('#increase').on('tap',function(){
		var num = oInp.val()
		num++;
console.log(num,44444);
		if(num > kucunNum){
			num = kucunNum;
		}
		oInp.val(num);
	});

	$('#reduce').on('tap', function(){
		var num = oInp.val()
		num--;
		if(num < 1){
			num = 1;
		}
		oInp.val(num);
	});
```



# 开发购物车

## 添加商品到购物车

```javascript
/**
 * 加入购物车
 * 1.获取加入购物车按钮 并添加点击事件
 * 2.判断用户是否选择了尺码
 * 3.调用加入购物车接口
 * 4.提示用户 加入购物车成功 是否跳转到购物车页面
 */

$('#addCart').on('tap', function(){
 if(!size){
  alert('请选择尺码');
  return;
 }
 $.ajax({
  url: '/cart/addCart',
  type: 'post',
  data: {
   productId: productId,
   num: kucunNum,
   size: size
  },
  success: function(res){
   console.log(res);
   if(res.success){
    mui.confirm("加入购物车成功,跳转到购物车?", function(message){
     if(message.index){
      // 跳转到购物车
      location.href = "cart.html";
     }
    })
   }else if(res.error==400){
    mui.toast('请先登录');
    location.href = "login.html";
   }
  }
 });
});
```

# 作业

- 购物车列表，同收货地址