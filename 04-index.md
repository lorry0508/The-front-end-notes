# 前台页面的主页面

## 最新发布功能

页面中很多功能都是类似的，在这个页面上我们只要完成主页面的最新发布功能。其他部分的功能就比较类似了

最新发布功能要求我们需要把数据库中的最新添加的文章筛选出来罗列在最新发布区域。首先观察静态结构

```html
<div class="panel new">
	<h3>最新发布</h3>
	<div class="entry"> ... </div>
    <div class="entry"> ... </div>
  	<div class="entry"> ... </div>
  	....
</div>
```

最新发布区域的每个类名为 `entry` 的div就是每一篇文章的结构，所以我们只需要把需要的数据读取出来，动态生成这样的结构即可。

首先我们在index.php中先把连接数据库的代码完成。

```php
	//连接数据库
    $connect = mysqli_connect(DB_HOST,DB_USER,DB_PWD,DB_NAME);
    $sql = "";
    //执行查询
    $postResult = mysqli_query($connect,$sql);
    //转换为二维数组
    $postArr = [];
    while ($row = mysqli_fetch_assoc($postResult)) {
        $postArr[] = $row;
    }
```

此时sql语句还没有完成，写sql语句需要我们观察一下，在页面中需要哪些数据

![](media/newpub-sql.png)

文章的很多数据都可以从`posts`表中获取，分类相关的数据可以联表从`categories` 表中获取,作者相关的数据可以从`users`表中获取,评论量要从`comments` 表格里获取。所以我们的sql语句可以写成

```mysql
# 分别从不同的表中查询不同的字段
SELECT p.title,p.created,p.content,p.views,p.likes,p.feature,c.`name`,u.nickname,
	# 根据文章id到comments表格中查找对应的评论数量
    (SELECT count(id) FROM comments WHERE post_id = p.id) as commentsCount
     FROM posts p
     # 联表查询
    LEFT JOIN categories c on c.id = p.category_id
    LEFT JOIN users u on u.id = p.user_id
    # 筛选一下不展示的分类
    WHERE p.category_id != 1
```

并且我们要找的是最新的文章，可以倒序排序一下，以及我们不是要所有的文章，只要最新的几篇即可，所以可以限定一下数量，所以最终sql语句为：

```mysql
# 分别从不同的表中查询不同的字段
SELECT p.title,p.created,p.content,p.views,p.likes,p.feature,c.`name`,u.nickname,
	# 根据文章id到comments表格中查找对应的评论数量
    (SELECT count(id) FROM comments WHERE post_id = p.id) as commentsCount
     FROM posts p
     # 联表查询
    LEFT JOIN categories c on c.id = p.category_id
    LEFT JOIN users u on u.id = p.user_id
    # 筛选一下不展示的分类
    WHERE p.category_id != 1
    # 倒序排列
    order BY p.created desc
    # 限定数量
    LIMIT 5
```

记得把sql语句复制回到php代码中

```php
//连接数据库
    $connect = mysqli_connect(DB_HOST,DB_USER,DB_PWD,DB_NAME);
    //sql语句
    $sql = "SELECT p.title,p.created,p.content,p.views,p.likes,p.feature,c.`name`,u.nickname,
    (SELECT count(id) FROM comments WHERE post_id = p.id) as commentsCount
     FROM posts p
    LEFT JOIN categories c on c.id = p.category_id
    LEFT JOIN users u on u.id = p.user_id
    WHERE p.category_id != 1
    order BY p.created desc
    LIMIT 5";
    //执行查询
    $postResult = mysqli_query($connect,$sql);
    //转换为二维数组
    $postArr = [];
    while ($row = mysqli_fetch_assoc($postResult)) {
        $postArr[] = $row;
    }
```

最终在最新发布区域把数据动态渲染

```php+HTML
<div class="panel new">
  <h3>最新发布</h3>      
  <!-- 遍历数组，生成结构 -->
  <?php foreach($postArr as $value) :?>
  <div class="entry">
    <div class="head">
      <span class="sort"><?php echo $value['name'] ?></span>
      <a href="javascript:;"><?php echo $value['title'] ?></a>
    </div>
    <div class="main">
      <p class="info"><?php echo $value['nickname']; ?> 发表于 <?php echo $value['created']; ?></p>
      <p class="brief"><?php echo $value['content']; ?></p>
      <p class="extra">
        <span class="reading">阅读(<?php echo $value['views']; ?>)</span>
        <span class="comment">评论(<?php echo $value['commentsCount']; ?>)</span>
        <a href="javascript:;" class="like">
          <i class="fa fa-thumbs-up"></i>
          <span>赞(<?php echo $value['likes']; ?>)</span>
        </a>
        <a href="javascript:;" class="tags">
          分类：<span><?php echo $value['name'] ?></span>
        </a>
      </p>
      <a href="javascript:;" class="thumb">
        <img src="static/uploads/hots_2.jpg" alt="">
      </a>
    </div>
  </div>
  <?php endforeach ?>
</div>
</div>
```

