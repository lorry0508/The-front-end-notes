# 后台页面的分类管理

## 静态结构中需要修改的地方

对于分类数据而言，我们需要展示、新增和修改的数据有三个，而静态结构中只给了两个，所以我们对比数据库之后发现，还缺少一个类名的显示，因此我们把类名这一列加上

以下是原来的静态结构：这个结构是少了一列的。

```html
<table class="table table-striped table-bordered table-hover">
  <thead>
    <tr>
      <th class="text-center" width="40"><input type="checkbox"></th>
      <th>名称</th>
      <th>Slug</th>
      <th class="text-center" width="100">操作</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="text-center"><input type="checkbox"></td>
      <td>未分类</td>
      <td>uncategorized</td>
      <td class="text-center">
        <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>
        <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>
      </td>
    </tr>
    <tr>
      <td class="text-center"><input type="checkbox"></td>
      <td>未分类</td>
      <td>uncategorized</td>
      <td class="text-center">
        <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>
        <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>
      </td>
    </tr>
    <tr>
      <td class="text-center"><input type="checkbox"></td>
      <td>未分类</td>
      <td>uncategorized</td>
      <td class="text-center">
        <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>
        <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>
      </td>
    </tr>
  </tbody>
</table>
```

将表格的静态结构修改为：相比原来的，多了一列类名的展示

```html
<table class="table table-striped table-bordered table-hover">
  <thead>
    <tr>
      <th class="text-center" width="40"><input type="checkbox"></th>
      <th>名称</th>
      <th>Slug</th>
      <th>类名</th>
      <th class="text-center" width="100">操作</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td class="text-center"><input type="checkbox"></td>
      <td>未分类</td>
      <td>uncategorized</td>
      <td>fa-fire</td>
      <td class="text-center">
        <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>
        <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>
      </td>
    </tr>
    <tr>
      <td class="text-center"><input type="checkbox"></td>
      <td>未分类</td>
      <td>uncategorized</td>
      <td>fa-fire</td>
      <td class="text-center">
        <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>
        <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>
      </td>
    </tr>
    <tr>
      <td class="text-center"><input type="checkbox"></td>
      <td>未分类</td>
      <td>uncategorized</td>
      <td>fa-fire</td>
      <td class="text-center">
        <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>
        <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>
      </td>
    </tr>
  </tbody>
</table>
```

还有左边的表单部分，也是少一个类名的输入框的，把这个类名的输入框加上，加在保存之前

```html
<div class="form-group">
  <label for="slug">类名</label>
  <input id="classname" class="form-control" name="classname" type="text" placeholder="类名">
  <p class="help-block">https://zce.me/category/<strong>类名</strong></p>
</div>
```

还有最后一部分是，我们在点击编辑的时候，是有`编辑完成`和`取消编辑`两个按钮的，所以在按钮那里也加上这两个按钮

```html
<input style="display: none;" id="btn-edit" type="button" value="编辑完成" class="btn btn-primary">
<input style="display: none;" id="btn-cancle" type="button" value="取消编辑" class="btn btn-primary">
```

---

## 完成分类数据的加载

后台项目中，我们采用ajax请求的方式请求数据，因为分类数据在一开始加载页面之后就会显示在页面中，所以我们可以在页面加载完毕之后把数据请求回来，动态生成结构。先在页面中完成一个ajax请求

```javascript
$(function(){
  //在页面加载完毕之后，就要展示数据一开始就要获取数据
  $.ajax({
    type: "POST",
    url : "api/_getCategoryData.php",
    success : function (res) { ... }
    }
  });  
});
```

我们现在在api文件夹中新建一个`_getCategoryData.php`文件，在里面连接数据库获取分类的数据

```php
require_once '../../config.php';
require_once '../../functions.php';
// 连接数据库查询出分类相关的数据，返回给前端即可

// 1 连接数据库
$connect = connect();
// 2 sql语句
$sql = "SELECT * FROM categories";
// 3 执行查询·
$queryResult = query($connect,$sql);
// 4 把结果返回给前端
$response = ["code"=>0,"msg"=>"操作失败"];
if($queryResult){
  $response["code"] = 1;
  $response["msg"] = "操作成功";
  $response["data"] = $queryResult;
}
// 以json格式返回数据
header("content-type: application/json");
echo json_encode($response);
```

此时在前端我们已经可以得到数据了，所以动态渲染即可

```javascript
$.ajax({
  type: "POST",
  url : "api/_getCategoryData.php",
  success : function (res) {
    // 请求成之后，把数据动态的渲染出来
    if(res.code == 1){
      //在请求成功的情况下，动态的渲染数据表格
      // 遍历数据数组，生成每一行，添加到表格中即可
      var str = "";
      var data = res.data;
      $.each(data,function(i,e){
        str += '<tr>\
        <td class="text-center"><input type="checkbox"></td>\
        <td>'+ e.name +'</td>\
        <td>'+ e.slug +'</td>\
        <td>'+ e.classname +'</td>\
        <td class="text-center">\
        <a href="javascript:;" data-categoryId="'+ e.id +'" class="btn btn-info btn-xs edit">编辑</a>\
        <a href="javascript:;" class="btn btn-danger btn-xs del">删除</a>\
        </td>\
        </tr>';
      });
      // 把字符串变成元素，追加到表格中即可
      $(str).appendTo('tbody');
    }
  }
});
```

---

## 完成分类的添加功能

当用户要添加分类的时候，在页面左边的输入框中输入数据。此时需要验证的东西不多，只要保证用户输入的数据不为空即可。所以给添加按钮注册点击事件，并且验证数据输入是否为空。

```javascript
// 点击按钮添加分类数据
$("#btn-add").on("click",function(){
  // 1 收集表单数据 - 验证表单数据是否填写完整
  var name = $("#name").val();
  var slug = $("#slug").val();
  var classname = $("#classname").val();
  // 如果数据填写不完整，就提示用户要填写完整才能添加
  if(name == ""){
    // 提示用户分类的名称不能为空
    $("#msg").text("分类的名称不能为空");
    $(".alert").show();
    return;
  }
  if(slug == ""){
    // 提示用户分类的别名不能为空
    $("#msg").text("分类的别名不能为空");
    $(".alert").show();
    return;
  }
  if(classname == ""){
    // 提示用户分类的图标样式不能为空
    $("#msg").text("分类的图标样式不能为空");
    $(".alert").show();
    return;
  }
}
```

数据简单的验证完毕之后，就可以把数据发送到服务端进行验证了。在添加按钮的点击事件处理程序里添加ajax提交的代码

```javascript
// 2 发送到服务端进行添加
$.ajax({
  url : "api/_addCategory.php",
  data : $("#data").serialize(),
  type : "POST",
  success : function(res){ ... }
});
```

我们在`admin/api`目录下建立`_addCategory.php`文件用来处理添加分类的请求

### 新增分类的流程

```flow
s=>start: 开始
o1=>operation: 把数据发送到服务端
c1=>condition: 是否已经存在相同的分类
o2=>operation: 把数据插入到数据库中
o3=>operation: 提示错误
o4=>operation: 把操作结果返回给前端
e=>end: 结束
s->o1->c1
c1(no)->o2->o4
c1(yes)->o3->o4
o4->e
```

根据流程图的说明，我们在`_addCategory.php`中完成代码

```php
require_once '../../config.php';
require_once '../../functions.php';
// 获取分类的名称
$name = $_POST['name'];
// 连接数据库
$connect = connect();
// 判断用户新增的分类是否跟数据库中已经存在的分类重名，如果重名了，不允许添加
// 如果根据用户新增的分类名称查找的条数大于0，就是已经存在
$countSql = "SELECT count(*) as count FROM categories WHERE `name` = '{$name}'";
$countResult = query($connect,$countSql);
$count = $countResult[0]['count'];// 这是查询的是否存在的条数
// 如果存在就提示用户是不能添加的
$response = ['code'=>0,"msg"=>"操作失败"];
if($count > 0){
  $response['msg'] = "分类名称已经存在，不能重复添加";
}else {
  // 如果不存在，就继续新增的逻辑
  // 准备新增的sql语句
  $keys = array_keys($_POST);
  $values = array_values($_POST);
  $sqlAdd = "INSERT into categories (". implode(",",$keys) .") VALUES('". implode("','",$values) ."')";
  // 执行新增的sql语句
  $addResult = mysqli_query($connect,$sqlAdd);
  if($addResult){
    $response['code'] = 1;
    $response['msg'] = '操作成功';
  }
}		
header("content-type: application/json");
// 把结果返回给前端
echo json_encode($response);
```

完成服务端的代码之后，我们在前端的请求就有相应的数据了，可以完成ajax的代码了。在前端我们判断一下，如果添加的结果是成功的，就直接生成一个对应的结构在页面中。

```javascript
// 2 发送到服务端进行添加
$.ajax({
  url : "api/_addCategory.php",
  data : $("#data").serialize(),
  type : "POST",
  success : function(res){
    // 根据返回的结果，动态生成一行数据追加到表格的末尾即可
    // 判断返回的结果是否成功，如果是成功的，动态生成表格
    if(res.code == 1){
      // 生成结构
      var str = '<tr>\
      <td class="text-center"><input type="checkbox"></td>\
      <td>'+ name +'</td>\
      <td>'+ slug +'</td>\
      <td>'+ classname +'</td>\
      <td class="text-center">\
      <a href="javascript:;" class="btn btn-info btn-xs">编辑</a>\
      <a href="javascript:;" class="btn btn-danger btn-xs">删除</a>\
      </td>\
      </tr>';
      $(str).appendTo('tbody');
    }
  }
});
```

## 抽离公用代码

不难发现，我们的新增操作不仅仅只有分类有，所以新增的操作我们也是可以封装起来的。而可以重复的部分主要是在使用sql语句操作数据库的流程上面。所以我们可以把新增的逻辑封装到`functions.php`中。

```php
/*
封装插入数据的操作
*/
function insert($connect,$table,$arr){
  $keys = array_keys($arr);
  $values = array_values($arr);
  $sqlAdd = "INSERT into {$table} (". implode(",",$keys) .") VALUES('". implode("','",$values) ."')";
  // 执行新增的sql语句
  $addResult = mysqli_query($connect,$sqlAdd);
  return $addResult;
}
```

这样封装起来之后，我们的新增的代码就可以简洁很多。在`_addCategory.php`中的代码可以修改为

```php
require_once '../../config.php';
require_once '../../functions.php';
// 获取分类的名称
$name = $_POST['name'];
// 连接数据库
$connect = connect();
// 判断用户新增的分类是否跟数据库中已经存在的分类重名，如果重名了，不允许添加
// 如果根据用户新增的分类名称查找的条数大于0，就是已经存在
$countSql = "SELECT count(*) as count FROM categories WHERE `name` = '{$name}'";
$countResult = query($connect,$countSql);
$count = $countResult[0]['count'];// 这是查询的是否存在的条数
// 如果存在就提示用户是不能添加的
$response = ['code'=>0,"msg"=>"操作失败"];
if($count > 0){
  $response['msg'] = "分类名称已经存在，不能重复添加";
}else {
  // 调用封装好的函数完成插入操作
  $addResult = insert($connect,"categories",$_POST);
  // 判断添加的结果是否成功，如果成功，告知前端可以动态的生成表格结构了
  if($addResult){
    $response['code'] = 1;
    $response['msg'] = '操作成功';
  }
}		
header("content-type: application/json");
// 把结果返回给前端
echo json_encode($response);
```

## 完成编辑的功能

在我们点击表格中的编辑按钮的时候，需要对对应的分类数据进行编辑。

### 编辑的流程图

```flow
s=>start: 开始
editClick=>operation: 点击编辑按钮
showInfo=>operation: 把数据填充到输入框
editData=>operation: 修改数据
saveClick=>operation: 点击保存完成
sendData=>operation: 发送数据到服务端
updateData=>operation: 更新数据
sendBack=>operation: 返回结果
e=>end: 结束
s->editClick->showInfo->editData->saveClick->sendData->updateData->sendBack->e
```

### 完成编辑的代码

根据流程图，先给编辑按钮注册事件。但是所有的编辑按钮都是动态生成的，所以使用委托的方式注册。

```javascript
$("tbody").on("click",".edit",function(){ ... });
```

在处理程序中，先获取当前行，取出每个单元格的数据，填充到左边的表单，同时把保存的取消的按钮显示，把添加的按钮隐藏

```javascript
// 点击事件处理程序里面的代码
// 先获取点击按钮对应的数据的id
var categoryId = $(this).attr("data-categoryid");
// console.log(categoryId);
// 把对应的id存储到编辑完成按钮身上
$("#btn-edit").attr("data-categoryid",categoryId);
// 先在这里把添加按钮隐藏，把编辑完成和取消编辑的按钮显示出来
$("#btn-add").hide();
$("#btn-edit").show();
$("#btn-cancle").show();
// 把点击的按钮对应的数据 填充到对应的输入框
var name = $(this).parents("tr").children().eq(1).text();
var slug = $(this).parents("tr").children().eq(2).text();
var className = $(this).parents("tr").children().eq(3).text();
// 把获取到的内容填充到对应的输入框中
$("#name").val(name);
$("#slug").val(slug);
$("#classname").val(className);
```

之后在用户编辑完数据之后，用户会点击`编辑完成`按钮，所以我们得给编辑完成按钮注册事件

```javascript
// 编辑完成按钮的点击事件
$("#btn-edit").on("click",function(){ ... })
```

我们在点击保存的时候，同样需要验证一下数据是否为空

```javascript
$("#btn-edit").on("click",function(){
  // 1 收集表单数据 - 验证表单数据是否填写完整
  var name = $("#name").val();
  var slug = $("#slug").val();
  var classname = $("#classname").val();
  // 如果数据填写不完整，就提示用户要填写完整才能添加
  if(name == ""){
    // 提示用户分类的名称不能为空
    $("#msg").text("分类的名称不能为空");
    $(".alert").show();
    return;
  }
  if(slug == ""){
    // 提示用户分类的别名不能为空
    $("#msg").text("分类的别名不能为空");
    $(".alert").show();
    return;
  }
  if(classname == ""){
    // 提示用户分类的图标样式不能为空
    $("#msg").text("分类的图标样式不能为空");
    $(".alert").show();
    return;
  }
}
```

如果验证过数据都不为空之后，就可以把数据提交到服务端进行修改了，但是要注意的是，我们要修改数据肯定要根据这条数据的id修改，所以的先得到这个数据的id，带回服务端，但是我们这个时候是没有数据的id的，所以得在一开始生成数据的时候把每一行数据对应的id存储起来，我们推荐把id存储在对应的行上面。即要把最开始展示数据的部分修改一下：

在一开始请求数据的ajax中修改一下tr的生成

```javascript
$.ajax({
  type: "POST",
  url : "api/_getCategoryData.php",
  success : function (res) {
    // 请求成之后，把数据动态的渲染出来
    if(res.code == 1){
      //在请求成功的情况下，动态的渲染数据表格
      // 遍历数据数组，生成每一行，添加到表格中即可
      var str = "";
      var data = res.data;
      $.each(data,function(i,e){
        //在这里把分类id加到tr的自定义属性中
        str += '<tr data-categoryId="'+ e.id +'">\
        <td class="text-center"><input type="checkbox"></td>\
        <td>'+ e.name +'</td>\
        <td>'+ e.slug +'</td>\
        <td>'+ e.classname +'</td>\
        <td class="text-center">\
        <a href="javascript:;" data-categoryId="'+ e.id +'" class="btn btn-info btn-xs edit">编辑</a>\
        <a href="javascript:;" class="btn btn-danger btn-xs del">删除</a>\
        </td>\
        </tr>';
      });
      // 把字符串变成元素，追加到表格中即可
      $(str).appendTo('tbody');
    }
  }
});
```

当生成的结构中有保存分类id之后，就可以获取到分类的id了。

```javascript
// 编辑完成按钮的点击事件
$("#btn-edit").on("click",function(){
  //把表单中的数据更新到数据库中
  // 先获取点击按钮对应的数据的id
  var categoryId = $(this).attr("data-categoryid");
  // 1 收集表单数据 - 验证表单数据是否填写完整
  var name = $("#name").val();
  var slug = $("#slug").val();
  var classname = $("#classname").val();
  // 如果数据填写不完整，就提示用户要填写完整才能添加
  if(name == ""){
    // 提示用户分类的名称不能为空
    $("#msg").text("分类的名称不能为空");
    $(".alert").show();
    return;
  }
  if(slug == ""){
    // 提示用户分类的别名不能为空
    $("#msg").text("分类的别名不能为空");
    $(".alert").show();
    return;
  }
  if(classname == ""){
    // 提示用户分类的图标样式不能为空
    $("#msg").text("分类的图标样式不能为空");
    $(".alert").show();
    return;
  }
  // 把id和修改过后的数据，发送到服务端，根据id对数据进行更新
  $.ajax({
    url : "api/_updateCategory.php",
    type : "POST",
    data : {name : name , slug : slug , classname : classname , id : categoryId},
    success : function(res){
      // 如果修改成功
      if(res.code == 1){ ... }
  });
})
```

此时我们在`admin/api`目录下新建一个`_updateCategory.php`文件，处理修改的逻辑

```php
require_once '../../config.php';
require_once '../../functions.php';
// 第一步： 先获取id和一些要修改的数据
$id = $_POST["id"];
// 连接数据库
$connect = connect();
// sql语句
$sql = "update categories set ";
// 遍历$_POST之前，要把id先从数组中去掉
unset($_POST["id"]);
// 遍历$_POST数组，把每个建和值拼接到sql语句中
foreach ($_POST as $key => $value) {
  $sql .= "{$key}='{$value}',";
}
// 循环导致sql语句的末尾多一个逗号，先把逗号去掉
$sql = substr($sql,0,-1);
$sql .=" where id = '{$id}'";
// 执行sql语句
$result = mysqli_query($connect,$sql);
// 把结果返回给前端
$response = ['code'=>0,"msg"=>"操作失败"];
if($result){
  $response['code'] = 1;
  $response['msg'] = "操作成功";
}
// 以json格式返回前端
header("content-type: application/json");
echo json_encode($response);
```

此时在前端接收到返回的结果后，在成功的情况下，我们需要把表单清空，并且把数据更新到右边的表格中。

但是这个时候是不知道我们之前点击编辑的是哪一行的，所以我们在外面定义一个变量用来保存点击编辑的是哪一行。

```javascript
// 在入口函数中定义一个变量，用来保存当前点击的编辑的行
var currentRow ;
```

接着处理更新成功的逻辑

```javascript
//这里是ajax请求修改数据的代码
// 把id和修改过后的数据，发送到服务端，根据id对数据进行更新
$.ajax({
  url : "api/_updateCategory.php",
  type : "POST",
  data : {name : name , slug : slug , classname : classname , id : categoryId},
  success : function(res){
    // 如果修改成功
    if(res.code == 1){
      // 要把两个编辑的按钮隐藏，把添加的按钮显示
      $("#btn-add").show();
      $("#btn-edit").hide();
      $("#btn-cancle").hide();
      // 清空之前先保存之前修改过后的内容
      var name = $("#name").val();
      var slug = $("#slug").val();
      var className = $("#classname").val();
      // 清空输入框
      $("#name").val("");
      $("#slug").val("");
      $("#classname").val("");
      // 把对应的数据更新到表格中
      currentRow.children().eq(1).text(name);
      currentRow.children().eq(2).text(slug);
      currentRow.children().eq(3).text(className);
    }
  }
});
```



若是用户点击了编辑之后，又取消了编辑，我们就把表单情况，恢复到点击编辑前的状态。

```javascript
// 取消编辑
$("#btn-cancle").on("click",function(){
  // 把对应的按钮显示和隐藏
  $("#btn-add").show();
  $("#btn-edit").hide();
  $("#btn-cancle").hide();
  // 情况输入框
  $("#name").val("");
  $("#slug").val("");
  $("#classname").val("");
})
```



## 完成删除的功能

先给删除按钮注册点击事件，同样因为是动态生成的，所以我们需要使用委托的方式注册。

```javascript
/*
点击删除按钮，删除数据
*/
$("tbody").on("click",".del",function(){ ... });
```

删除功能比较简单，我们只需要根据分类的id对数据进行删除即可，所以我们在点击事件里面使用ajax请求把分类的id发送到服务端即可

```javascript
$("tbody").on("click",".del",function(){
  // 先把当前点击的行记录下来，当删除成功的时候，我们就把这一行删除掉
  var row = $(this).parents("tr");
  // 根据当前点击的按钮对应的行，得到其对应的id，在数据库中删除数据
  // 先获取要删除的数据的id
  var categoryId = row.attr("data-categoryId");
  // 把要删除的id发送回服务端
  $.ajax({
    type : "POST",
    url : "api/_delCategory.php",
    data : { id : categoryId},
    success : function(res){ ... }
  });
})
```

所以此时我们在`admin/api`目录下新建`_delCategory.php`

```php
require_once '../../config.php';
require_once '../../functions.php';
//  获取要删除的数据的id
$id = $_POST['id'];
// 连接数据库
$connect = connect();
// sql语句
$sql = "DELETE FROM categories WHERE id = '{$id}'";
// 执行
$result = mysqli_query($connect,$sql);
// 返回前端的数据
$response = ["code"=>0,"msg"=>"操作失败"];
if($result){
  $response["code"] = 1;
  $response["msg"] = "操作成功";
}
// 以json格式返回数据
header("content-type: application/json");
echo json_encode($response);
```

根据返回的结果，成功的时候，我们就把当前行删除掉即可。

```javascript
// 删除按钮点击事件处理程序里的代码
// 把要删除的id发送回服务端
$.ajax({
  type : "POST",
  url : "api/_delCategory.php",
  data : { id : categoryId},
  success : function(res){
  // 如果删除成功,也要把对应的行给删除掉
    if(res.code == 1){
    //要把对应的行给移除掉
    row.remove();
    }
  }
});
```

## 批量删除功能

## 全选按钮功能

当我们勾选表格中的全选多选框，会选中所有数据，以及会有一个批量显示按钮出现

给全选多选框注册一个点击事件

```javascript
/*
全选功能的实现
*/
$("thead input").on('click', function() {
  // 控制别的多选框跟我的当前的选中状态一样
  // 获取自己的选中状态
  var status = $(this).prop("checked");
  // 控制别的多选框
  $("tbody input").prop("checked",status);

  // 判断当前是否选中，如果选中，显示删除多个
  if(status){
    $("#delAll").show();
  }else {
    $("#delAll").hide();
  }
});
```

##多行选批量删除功能

超过两行被选中，也会出现批量删除按钮，所以也要给每一行的多选框注册事件，同样要用委托的方式注册

```javascript
/*
使用委托的方式为别的多选框注册点击事件
*/
$("tbody").on("click","input",function(){
  // 控制全选的多选框是否选中，只有当所欲的多选框都选中，全选才选中
  // 获取全选多选框
  var all = $("thead input");
  var cks = $("tbody input");
  // 如果cks里面的所有的多选框都选中了，全选也选中
  all.prop("checked",cks.size() == $("tbody input:checked").size());
  // 选中的多选框超过两个，就显示批量删除
  if($("tbody input:checked").size() >= 2){
    $("#delAll").show();
  }else {
    $("#delAll").hide();
  }
});
```

## 点击批量删除的实现

给批量删除按钮注册一个点击事件

```javascript
/*
点击批量删除
 */
$("#delAll").on("click",function(){
  // 准备好的收集多个id的数组
  var ids = [];
  // 收集所有选中的id，发送到服务器进行数据的删除
  var cks = $("tbody input:checked");
  // 遍历伪数组，找到所有被选中的id
  cks.each(function(index, el) {
    var id = $(el).parents("tr").attr("data-categoryId");
    ids.push(id);
  });
  // 把数据发送回服务端，删除数据
  $.ajax({
    url : "api/_delCategoriese.php",
    type : "POST",
    data : { ids : ids},
    success : function(res){
      // 如果删除成功了，要把对应的行也移除掉
      if(res.code == 1){
        cks.parents("tr").remove();
      }
    }
  });
})
});
```

需要一个`admin/api`目录下的`_delCategoriese.php`文件处理请求

```php
require_once '../../config.php';
require_once '../../functions.php';
// 获取从前端传递回来的要删除的多个id
$ids = $_POST["ids"];
// 连接数据库
$connect = connect();
// 删除的sql语句
$sql = "DELETE FROM categories WHERE id in ('". implode("','",$ids) ."')";
// 执行sql语句
$result = mysqli_query($connect,$sql);
// 把结果返回
$response = ["code"=>0,"msg"=>"操作失败"];
if($result){
  $response['code'] = 1;
  $response['msg'] = "操作成功";
}
header("content-type:application/json");
echo json_encode($response);
```



























