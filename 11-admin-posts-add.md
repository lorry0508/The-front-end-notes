# 后台页面的文章新增页面

## 功能说明

文章的新增功能，说白了也是往数据库中插入一条数据罢了。之前的分类新增已经完成过一次，所以逻辑是很类似的。但是文章的新增比分类的新增多了两个小功能

   	1. 文章封面图片的上传
  2. 文章内容富文本编辑器

所以这里我们额外把图片上传和富文本编辑器功能完成，剩下的就可以参考分类新增的写法搞定了。

## 封面图片上传功能

### 文件上传的流程

```flow
s=>start: 开始
o1=>operation: 选择要上传的图片
o2=>operation: 使用ajax将选中的图片上传到服务器
o3=>operation: 在服务端得到上传的图片
o4=>operation: 将得到的图片保存到指定的目录
c1=>condition: 是否保存成功
o5=>operation: 将图片的服务端路径返回给前端
o6=>operation: 提示用户错误信息
e=>end: 结束
s->o1->o2->o3->o4->c1
c1(yes)->o5->e
c1(no)->o6->e
```

### 编码实现

​	手下我们需要先点文件按钮选中一张图片，值的注意的是，我们这个时候不能给按钮注册点击事件因为在点击事件完成的时候，文件还没选中，因此是得不到想要上传的图片的。这里我们要上传图片的话，要注册的事件是：change事件。change事件会在input的value产生改变的时候触发。而文件按钮的value就是选中的文件，因此当value改变，就是选中了要上传的图片。

​	首先给按钮注册change事件。

```javascript
$(function(){
  //给文件上传按钮注册事件
  $("#feature").on("change",function(){});  
});
```

​	然后在事件处理程序中获取要上传的图片。按钮对象身上有一个files伪数组对象，里面存储着我们选中的图片，取出第0张就是我们选中的图片。

```javascript
$("#feature").on("change",function(){
  // 获取到要上传的文件
  var file = this.files[0];
});  
```

​	之后要把这个图片放到FormData对象里面，否则ajax是无法把图片带回服务端的。[FormData对象的介绍](https://developer.mozilla.org/en-US/docs/Web/API/FormData/FormData)

```javascript
$("#feature").on("change",function(){
  // 获取到要上传的文件
  var file = this.files[0];
  // 创建一个FormData对象，用来把文件装起来
  var data = new FormData();
  // 调用自带的方法把图片添加到对象中  
  data.append("file",file);
});  
```

​	然后我们通过jquery的ajax将图片上传到服务端。需要注意的是，jquery默认是无法上传文件的，需要额外设置两个参数：

   	1. contentType:false 要设置这个参数为false，jquery才会把文件带回服务端
  2. processData: false 要 告诉jquery不要把参数序列化

然后我们在成功上传之后，把图片的路径设置到在页面中准备好的图片标签上，用来预览图片。

```javascript
$.ajax({
  url : "api/_uploadFile.php",
  type : "POST",
  data : data,
  //  需要配置两个参数，设置jquery才能把文件带回服务端
  contentType : false, // 只有设置了这个参数，jquery才会把文件带回服务端
  processData : false, // 告诉jq不要序列化我们的参数
  success : function(res){
    if(res.code == 1){
      // 把图片预览
      $(".help-block").attr("src",res.src).show();
    }
  }
});
```

​	现在处理后台的代码。需要在api目录下新建一个`_uploadFile.php`文件。我们可以通过`$_FILES`得到我们从浏览器端上传回来的图片，但是我们不能直接把图片存储到指定目录，因为不同用户的图片可能同名，如果同名了，那么就会产生覆盖。所以我们采用`时间戳+随机数`的方式重命名图片。

```php
// 获取上传回来的文件
$file = $_FILES['file'];
// 生成一个不会重复的文件名
/*
	我们在服务端，为了保证上传的文件名不会同名

		时间戳 + 随机数 + 后缀名
*/
// 先得到文件的后缀名
// strrchr(字符串,符号)  得到的是某个符号最后一次出现在字符串的位置开始到字符串结尾的所有字符
$ext = strrchr($file['name'],".");

$fileName = time() . rand(10000,99999) . $ext;

// 把文件保存到指定的目录
$bool = move_uploaded_file($file['tmp_name'],"../../static/uploads/" . $fileName);

// 如果图片上传成功，返回图片的路径，让前端页面可以预览
$response = ['code'=>0,'msg'=>"操作失败"];
if($bool){
  $response['code'] = 1;
  $response['msg'] = '操作成功';
  // 把上传的图片的路径带回前端
  $response['src'] = '/static/uploads/' . $fileName;
}
// 以json格式返回
header("content-type: application/json");
echo json_encode($response);
```

## 富文本编辑器

​	在对文章的内容进行编辑的时候，如果只是单纯的文字，那么是非常单调的。我们平时在网上看到的文字，很少会只有纯文字的。那么，我们要如何做才能让文字更加丰富呢——富文本。

​	什么是富文本？

​		所谓富文本，即为有格式的文本。比如文章中有段落区分，有字体大小区分等等，甚至可以在文章中出现插图之类的，会更加丰富文章的形式。而我们要实现这样的格式，就要依赖于富文本插件。

​	在这个项目中用到的富文本插件，叫[CKEDITOR](https://ckeditor.com/ckeditor-4)，是一款非常不错的富文本插件。当然市面上也有其他的富文本插件，用法都是很类似的。

该插件的用法很简单，可以看下面的示例，也可以自己到官方网站上学习。

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>A Simple Page with CKEditor</title>
        <!--引入ckeditor插件-->
        <script src="../ckeditor.js"/>
    </meta></head>
    <body>
        <form>
            <textarea name="editor1" id="editor1" rows="10" cols="80">
                这个文本域将会被替换成为富文本编辑器
            </textarea>
            <script>
                // 调用API将文本域实例化为富文本编辑器
                // 用法： CKEDITOR.replace( 文本域的id );
                CKEDITOR.replace( 'editor1' );
            </script>
        </form>
    </body>
</html>
```

​	参照插件的用法，我们在页面中已经有了一个用于编辑文章内容的文本域`<textarea id="content" ... ></textarea>`，所以只要我们把下载好的插件引入页面，然后调用实例化的方法实例化编辑器即可。

```javascript
CKEDITOR.replace("content");
```

​	然后我们希望在点击保存按钮的时候把数据发送到服务端。但是我们发现直接调用jquery的序列化表单的方法`$().serialize()`方法把数据获取出来的时候，发现内容是空的。因为我们只是把富文本编辑器里面的内容编辑了，没有同步到文本域中，需要在点击保存按钮的时候，把文章内容先同步到文本域。

​	CKEDITOR也准备好了对应的API供我们操作。只要直接调用编辑器对象的`updateElement`方法就可以把数据同步回到文本域，我们才能获取到数据。具体的做法为：

```javascript
//CKEDITOR.instances 是当前页面中所有编辑器对象的实例集合，可以通过对应的id直接得到想要的编辑器对象。
//  CKEDITOR.instances.content 这个content就是我们实例化的时候使用的id，这个对象就是我们想要找的编辑器对象。
// 调用 updateElement方法可以把内容同步到对应的文本框
CKEDITOR.instances.content.updateElement();
```

所以对应的保存按钮的事件和处理函数为：

```javascript
// 点击保存按钮
$("#btn-save").on("click",function(){
  // 在收集数据之前，得先把富文本编辑器中的内容更新到文本域当值
  /*
    如果要把编辑器中的内容，更新到对应的文本域里面
    需要调用插件提供的一个方法： 编辑器对象.updateElement()
    获取编辑器对象：  CKEDITOR.instances.初始化的时候所使用的id
*/
  CKEDITOR.instances.content.updateElement();//把编辑器中的内容更新到文本域中
  // 点击保存按钮收集表单的数据，发送回服务端
  var data = $("#data-form").serialize();
  // 发送回服务端进行文章的新增操作
  $.ajax({
    url : "api/_addPost.php",
    data : data,
    type : "POST",
    success : function(res){
      // 处理新增成功的逻辑
    }
  });
});
```



























