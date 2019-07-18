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

# pc后台管理开发

## 登录与退出

知识点：

- 把submit按钮的class改为button，以防默认的表单提交
- 访问登录页时，如果已经登录则跳转到用户页
- 访问其他页时，如果未登录则跳转到登录页
- async:false 同步

login.js

```javascript
$.ajax({
 url: '/employee/checkRootLogin',
 type: 'get',
 async: false,
 success: function(res){

  if(res.success){

   location.href = "user.html";

  }
  
 }
});

$(function(){

 /**
  * 登录
  * 1.获取登录按钮并且添加点击事件
  * 2.获取用户输入的用户名和密码
  * 3.对用户输入的表单信息进行验证
  * 4.调用登录接口实现登录
  * 5.登录成功 跳转到用户管理页面
  */
 
 $('#login-button').on('click', function(){

  var username = $.trim($("[name='username']").val());
  var password = $.trim($("[name='password']").val());

  if(!username){
   alert("请输入用户名");
   return;
  }

  if(!password){
   alert("请输入密码");
   return;
  }

  $.ajax({
   url: '/employee/employeeLogin',
   type: 'post',
   data: {
    username: username,
    password: password
   },
   success: function(res){

    if(res.success){

     // 登录成功
     location.href = "user.html";

    }else {

     alert(res.message);

    }
    
   }
  });

 });

});
```

common.js

```javascript
/**
 * 登录拦截
 */

$.ajax({
 url: '/employee/checkRootLogin',
 type: 'get',
 async: false,
 success: function(res){

  if(res.error && res.error == 400){

   location.href = "login.html";

  }
  
 }
});

$(function(){

 /**
  * 退出
  * 1.获取退出按钮并且添加点击事件
  * 2.调用退出接口实现退出功能
  */

 $('.login_out_bot').on('click', function(){

  if(confirm("确定要退出吗?")) {

   $.ajax({
    url: '/employee/employeeLogout',
    type: 'get',
    success: function(res){

     if(res.success){

      location.href = "login.html";

     }else {

      alert(res.message);

     }

    }
   });

  }

 });
```



## 用户管理

### 获取用户列表

```javascript
/**
 * 获取用户列表
 */

$.ajax({
 url: '/user/queryUser',
 type: 'get',
 data: {
  page: 1,
  pageSize: 10
 },
 success: function(res) {
  console.log(res);

  var html = template("userTpl", res);

  $('#user-box').html(html)
  
 }
});
```

### 更新用户状态



## 一级分类

### 一级分类渲染

知识点：

- 相同逻辑抽成函数

```javascript
$(function(){

 /**
  * 获取一级分类数据并展示
  */
 
 var page = 1;
 var pageSize = 10;
 var totalPage = 0;

 // 页面一上来的时候 获取第一页的数据
 getData();
    
 $('#next').on('click',function(){
		page++;
		if(page > totalPage){
			page = totalPage;
			alert('已经是最后一页了');
			return;
		}
		getData();
	});

	$('#prev').on('click',function(){
		page--;
		if(page < 1){
			page = 1;
		    alert('已经是第一页了');
			return;
		}
		getData();
	});
 function getData(){
  $.ajax({
   url: '/category/queryTopCategoryPaging',
   type: 'get',
   data: {
    page: page,
    pageSize: pageSize
   },
   success: function(res) {

    console.log(res);

    // 总页数
    totalPage = Math.ceil(res.total / pageSize);

    var html = template("categoryFirstTpl", res);

    $('#categoryFirstBox').html(html);
    
   }
  });
 }
});
```



### 一级分类添加



```javascript
 /**
  * 添加一级分类
  * 1.获取一级分类保存按钮 并添加点击事件
  * 2.获取用户输入的分类名称 并且检验
  * 3.调用添加一级分类接口 实现功能
  * 4.刷新页面
  */
 
 $('#save').on('click', function(){

  var categoryFirstName = $.trim($("[name='categoryFirstName']").val());

  if(!categoryFirstName){
   alert("请输入一级分类名称");
   return;
  }

  $.ajax({
   url: '/category/addTopCategory',
   type: "post",
   data: {
    categoryName: categoryFirstName
   },
   success: function(res){

    if(res.success){

     location.reload();

    }
    
   }
  })

 });
```

## 二级分类

### 二级分类渲染

知识点：

- 获取一级分类列表



```javascript
/**
 * 二级分类添加
 * 1.获取一级分类的数据并显示在选择框中
 * 2.图片文件上传
 * 3.调用接口 实现二级分类数据添加
 */

$.ajax({
 url: '/category/queryTopCategoryPaging',
 type: 'get',
 data: {
  page: 1,
  pageSize: 100
 },
 success: function(res){
  
  var html = template("categoryFirstTpl",res);

  $('#categoryFirstBox').html(html);

 }
});
```

### 图片上传

知识点：

- jquery-fileuploader 引入需要的三个js文件



```html
<script src="assets/jquery-fileupload/jquery.ui.widget.js"></script>
<script src="assets/jquery-fileupload/jquery.iframe-transport.js"></script>
<script src="assets/jquery-fileupload/jquery.fileupload.js"></script>
<input
							type="file"
							class="form-control"
							id="fileUpload"
							data-url="/category/addSecondCategoryPic"
							name="file">
```

```javascript
// 上传图片
$('#fileUpload').fileupload({
      dataType: 'json',
      done: function (e, data) {
          
          console.log(data.result.picAddr);

          // 上传图片预览
          $('#preview').attr("src",data.result.picAddr);

          previewImg = data.result.picAddr;

      }
   });
```

### 二级分类添加

```javascript
var brandData = {
 brandName:"",
 categoryId:"",
 brandLogo:"",
 hot:0
}

$('#addCategory').on('click',function(){
 brandData.brandName = $('#brandName').val();
 brandData.categoryId = $('#categoryId').val();
 if(brandData.categoryId == '-1'){
  alert('请输入品牌所属分类');
  return;
 }
 if(!brandData.brandName){
  alert('请输入品牌名称')
  return;
 }
 if(!brandData.brandLogo){
  console.log(brandData.brandLogo)
  alert('请输入上传品牌图片');
  return;
 }
 $.ajax({
  url:'/category/addSecondCategory',
  type:'post',
  data:brandData,
  success:function(result){
   if(result.success){
    // location.reload();
   }else{
    alert('品牌添加失败');
    console.log(result);
   }
  }
 })
});
```

## 商品管理

### 商品展示

```javascript
var page = 1;
var pagesize = 10;
var totalPage = 0;
 
GetProduct();

function GetProduct(){
 $.ajax({
  type:'get',
  url:'/product/queryProductDetailList',
  data:{
   page:page,
   pageSize:pagesize
  },
  success:function(result){
   console.log(result,666);
   totalPage = Math.ceil(result.total/pagesize);
   $('#productBox').html(template('productTpl',{data:result}));
  }
 });
}
```

### 添加商品

知识点：

- 查询所有的二级分类
- 上传图片(注意input的name)，注意文档和后端代码

```javascript
$.ajax({
 url:'/category/querySecondCategoryPaging',
 type:'get',
 data:{
  page:1,
  pageSize:100
 },
 success:function(result){
  $('#brandBox').html(template('brandTpl',{data:result}));
  //console.log(result);
 }
});



/* 模态框隐藏就初始化表单值，防止再次进入图片还存在 */
$('.modal').on('hide.bs.modal', function () {
 location.reload();
});

var pic = [];
$('#fileUpload').fileupload({
    dataType: 'json',
    done: function (e, data) {
  if(pic.length<3){
   pic.push(data.result);
      $('#imgBox').html(template('imgsTpl',{data:pic}))
  }
    }
});

$('#addProduct').on('click',function(){
 var brandOptions = $('#brandOptions').val();
 var productName = $("#productName").val();
 var productDescription = $("#productDescription").val();
 var productNum = $("#productNum").val();
 var productSize = $("#productSize").val();
 var productOriginPrice = $("#productOriginPrice").val();
 var productNowPrice = $("#productNowPrice").val();


 if(brandOptions == -1){
  alert('请选择品牌');
  return;
 }

 if(!productName){
  alert('请输入产品名称');
  return;
 }

 if(!productDescription){
  alert('请输入产品描述');
  return;
 }

 if(!productNum){
  alert('请输入产品数量');
  return;
 }

 if(!productSize){
  alert('请输入产品尺寸');
  return;
 }

 if(!productOriginPrice){
  alert('请输入产品原价格');
  return;
 }

 if(!productNowPrice){
  alert('请输入产品折扣价');
  return;
 }

 if(pic.length == 0){
  alert('请上传产品图片');
  return;
 }

 $.ajax({
  type:'post',
  url:'/product/addProduct',
  data:{
   proName:productName,
   oldPrice:productOriginPrice,
   price:productNowPrice,
   proDesc:productDescription,
   size:productSize,
   statu:1,
   num:productNum,
   brandId:brandOptions,
   pic:pic
  },
  success:function(result){
   if(result.success){
    location.reload();
   }else{
    alert('商品添加失败');
   }
  }
 })
});
```

