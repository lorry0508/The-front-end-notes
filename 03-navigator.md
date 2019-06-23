# 动态生成导航数据

## 网站导航的数据来源

​	网站的导航数据是数据库中的 `categories` 表格，所以我们如果要把导航数据动态生成就必须连接数据库，要连接数据库就必须有数据库的相关信息，我们已经把数据库的相关信息配置在了`config.php` 当中。

## 引入config.php文件

原本是需要把`config.php`文件引入到_header.php中使用的，但是由于我们在`index.php`中也要用到数据库连接，而且我们也会在`index.php`中引入`_header.php`，那么`_header.php`中是可以访问由`index.php`声明的变量的，所以可以直接把`config.php`引入`index.php`中

```php
//index.php
<? 
	require_once 'config.php'; 
	///
?>
```

之后在`_header.php`中可以直接使用数据库的配置信息

## 在_header.php中完成导航数据的动态渲染

在页面中连接数据库和查询数据库

```php
// 连接数据库
  $connect = mysqli_connect(DB_HOST,DB_USER,DB_PWD,DB_NAME);
  // 准备sql语句
  $sql = "SELECT * FROM categories WHERE id != 1";
  // 执行查询操作
  $result = mysqli_query($connect,$sql);
  // 把数据集合转换为二维数组
  $arr = [];
  while ($row = mysqli_fetch_assoc($result)) {
      $arr[] = $row;
  }
```

在html代码中渲染导航数据

```php+HTML
<ul class="nav">
    <!-- 遍历二维数组，生成结构 -->
    <?php foreach ($arr as $value) : ?>
      <li><a href="list.php"><i class="fa <?php echo $value['classname'] ?>"></i><?php echo $value['name'] ?></a></li>
    <?php endforeach ?>
  </ul>
```

