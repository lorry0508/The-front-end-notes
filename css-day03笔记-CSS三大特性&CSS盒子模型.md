---
typora-copy-images-to: media
---

> 第01阶段.WEB基础：css-day03笔记-CSS三大特性&CSS盒子模型



# CSS-day03笔记-盒子模型



## CSS学习三大重点： CSS盒子模型、浮动、定位 



# 今日主题思路

<img src="media/day3.png" />





# 一、CSS三大特性

## 学习目标

- 理解
  - 能说出css样式冲突采取的原则
  - 能说出那些常见的样式会有继承

- 应用
  - 能写出CSS优先级的算法
  - 能会计算常见选择器的叠加值



## 1. CSS层叠性

<img src="media/20%E5%B1%82%E5%8F%A0%E6%80%A7.png">

- 概念：

  所谓层叠性是指多种CSS样式的叠加。

  是浏览器处理冲突的一个能力,如果一个属性通过两个相同选择器设置到同一个元素上，那么这个时候一个属性就会将另一个属性层叠掉

- 原则：

  - 样式冲突，遵循的原则是**就近原则。** 那个样式离着结构近，就执行那个样式。
  - 样式不冲突，不会层叠

```
CSS层叠性最后的执行口诀：  长江后浪推前浪，前浪死在沙滩上。
```

 <img src="media/hai.gif"  width="600" height="400" />

## 2. CSS继承性

<img src="media/21%E7%BB%A7%E6%89%BF%E6%80%A7.png">

- 概念：

  子标签会继承父标签的某些样式，如文本颜色和字号。

   想要设置一个可继承的属性，只需将它应用于父元素即可。

简单的理解就是：  子承父业。

- **注意**：
  - 恰当地使用继承可以简化代码，降低CSS样式的复杂性。比如有很多子级孩子都需要某个样式，可以给父级指定一个，这些孩子继承过来就好了。
  - 子元素可以继承父元素的样式（**text-，font-，line-这些元素开头的可以继承，以及color属性**）

```
CSS继承性口诀：  龙生龙，凤生凤，老鼠生的孩子会打洞。
```

 <img src="media/shu.gif" />

## 3. CSS优先级（重点）

<img src="media/22%E4%BC%98%E5%85%88%E7%BA%A7.png">

- 概念：

  定义CSS样式时，经常出现两个或更多规则应用在同一元素上，此时，

  - 选择器相同，则执行层叠性
  - 选择器不同，就会出现优先级的问题。

### 1) 权重计算公式

关于CSS权重，我们需要一套计算公式来去计算，这个就是 CSS Specificity（特殊性）

| 标签选择器             | 计算权重公式 |
| ---------------------- | ------------ |
| 继承或者 *             | 0,0,0,0      |
| 每个元素（标签选择器） | 0,0,0,1      |
| 每个类，伪类           | 0,0,1,0      |
| 每个ID                 | 0,1,0,0      |
| 每个行内样式 style=""  | 1,0,0,0      |
| 每个!important  重要的 | ∞ 无穷大     |

- 值从左到右，左面的最大，一级大于一级，数位之间没有进制，级别之间不可超越。 

- 关于CSS权重，我们需要一套计算公式来去计算，这个就是 CSS Specificity（特殊性）

- div {

  ```
  color: pink!important;  
  ```

  }

### 2) 权重叠加

我们经常用交集选择器，后代选择器等，是有多个基础选择器组合而成，那么此时，就会出现权重叠加。

就是一个简单的加法计算

- div ul  li   ------>      0,0,0,3
- .nav ul li   ------>      0,0,1,2
- a:hover      -----—>   0,0,1,1
- .nav a       ------>      0,0,1,1



 <img src="media/w.jpg" /> 注意： 

#### a. 权重是没有进位的

1. 数位之间没有进制 比如说： 0,0,0,5 + 0,0,0,5 =0,0,0,10 而不是 0,0, 1, 0， 所以不会存在10个div能赶上一个类选择器的情况。

#### b. 继承的权重是0

这个不难，但是忽略很容易绕晕。其实，**我们修改样式，一定要看该标签有没有被选中。**

① **如果选中了**，那么以上面的公式来计权重。**谁大听谁的**。 
② **如果没有选中**，那么权重是0，因为**继承的权重为0.**



# 二、盒子模型（重点）

## 学习目标

- 理解：
  - 能说出盒子模型有那四部分组成
  - 能说出内边距的作用以及对盒子的影响
  - 能说出padding设置不同数值个数分别代表的意思
  - 能说出块级盒子居中对齐需要的2个条件
  - 能说出外边距合并的解决方法
- 应用：
  - 能利用边框复合写法给元素添加边框
  - 能计算盒子的实际大小
  - 能利用盒子模型布局模块案例**（这个案例下次课讲）**

## 1.看透网页布局的本质

网页布局中，我们是如何把里面的文字，图片，按照美工给我们的效果图排列的整齐有序呢？

<img src="media/t.png" />

- **看透网页布局的本质：**
  -  **首先利用CSS设置好盒子的大小，然后摆放盒子的位置。**
  -  **最后把网页元素比如文字图片等等，放入盒子里面。**
  -  以上两步 就是网页布局的本质

 <img src="media/t1.png" />

我们明白了，盒子是网页布局的关键点，所以我们更应该弄明白 这个盒子有什么特点。

## 2. 盒子模型（Box Model）

- 所谓盒子模型：

  - 就是把HTML页面中的布局元素看作是一个矩形的盒子，也就是一个盛装内容的容器。

  <img src='./media/盒子模型.png'>

   <img src="media/boxs.png"  width="700" />

  **pink老师总结：**

  * 盒子模型有元素的内容、边框（border）、内边距（padding）、和外边距（margin）组成。
  * 盒子里面的文字和图片等元素是 内容区域
  * 盒子的厚度 我们成为 盒子的边框 
  * 盒子内容与边框的距离是内边距（类似单元格的 cellpadding)
  * 盒子与盒子之间的距离是外边距（类似单元格的 cellspacing）

**标准盒子模型**



 <img src='./media/标准盒子模型.png'>

## 3. 盒子边框（border） 

​	<img src='./media/盒子边框.png'>

- 语法：

~~~css
border : border-width || border-style || border-color 
~~~

| 属性           |      作用      |
| ------------ | :----------: |
| border-width | 定义边框粗细，单位是px |
| border-style |    边框的样式     |
| border-color |     边框颜色     |

- 边框的样式：
  - none：没有边框即忽略所有边框的宽度（默认值）
  - solid：边框为单实线(最为常用的)
  - dashed：边框为虚线  
  - dotted：边框为点线

### 1) 边框综合设置

```
border : border-width || border-style || border-color 
```

例如：

~~~css
 border: 1px solid red;  没有顺序  
~~~



### 2) 盒子边框写法总结表

很多情况下，我们不需要指定4个边框，我们是可以单独给4个边框分别指定的。

| 上边框                  | 下边框                      | 左边框                   | 右边框                    |
| :------------------- | :----------------------- | :-------------------- | :--------------------- |
| border-top-style:样式; | border-bottom-style:样式;  | border-left-style:样式; | border-right-style:样式; |
| border-top-width:宽度; | border- bottom-width:宽度; | border-left-width:宽度; | border-right-width:宽度; |
| border-top-color:颜色; | border- bottom-color:颜色; | border-left-color:颜色; | border-right-color:颜色; |
| border-top:宽度 样式 颜色; | border-bottom:宽度 样式 颜色;  | border-left:宽度 样式 颜色; | border-right:宽度 样式 颜色; |

### 3) 表格的细线边框

 <img src='./media/表格边框.png'>

- 通过表格的`cellspacing="0"`,将单元格与单元格之间的距离设置为0，

- 但是两个单元格之间的边框会出现重叠，从而使边框变粗

- 通过css属性：

  ~~~
  table{ border-collapse:collapse; }  
  ~~~

  - collapse 单词是合并的意思
  - border-collapse:collapse; 表示相邻边框合并在一起。

~~~css
<style>
	table {
		width: 500px;
		height: 300px;
		border: 1px solid red;
	}
	td {
		border: 1px solid red;
		text-align: center;
	}
	table, td {
		border-collapse: collapse;  /*合并相邻边框*/
	}
</style>
~~~

 <img src='./media/边框合并.png'>

## 4. 内边距（padding）

 <img src='./media/16内边距.png'>

### 1) 内边距：

​	padding属性用于设置内边距。 **是指 边框与内容之间的距离。**

### 2) 设置

| 属性             | 作用   |
| -------------- | :--- |
| padding-left   | 左内边距 |
| padding-right  | 右内边距 |
| padding-top    | 上内边距 |
| padding-bottom | 下内边距 |

当我们给盒子指定padding值之后， 发生了2件事情：

1. 内容和边框 有了距离，添加了内边距。
2. 盒子会变大了。

 <img src="media/w.jpg"/>**注意：  后面跟几个数值表示的意思是不一样的。**

我们分开写有点麻烦，我们可以不可以简写呢？

| 值的个数 | 表达意思                           |
| ---- | ------------------------------ |
| 1个值  | padding：上下左右内边距;               |
| 2个值  | padding: 上下内边距    左右内边距 ；      |
| 3个值  | padding：上内边距   左右内边距   下内边距；   |
| 4个值  | padding: 上内边距 右内边距 下内边距 左内边距 ； |

<img src='./media/顺时针.jpg'>

**课堂一练：**

请写出如下内边距：

1. 要求盒子有一个左边内边距是 5像素
2. 要求简写的形式写出  一个盒子，上下是 25像素   左右是15像素。
3. 要求简写的形式写出 一个盒子，  上内边距是 12像素  下内边距是 0  左内边距是 25像素  右内边距是 10像素

### 3) 课堂案例：  新浪导航

新浪导航栏的核心就是因为里面的字数不一样多，所以我们不方便给宽度，还是给padding ，撑开盒子的。

 <img src="media/al.gif" />

### 4) 内盒尺寸计算（元素实际大小）

<img src='./media/盒模型大小.png'>

- 宽度

  Element Height = content height + padding + border （Height为内容高度）

- 高度

   Element Width = content width + padding + border （Width为内容宽度）

-  盒子的实际的大小 =   内容的宽度和高度 +  内边距   +  边框   

### 5) 内边距产生的问题

- 问题

  <img src='./media/31padding问题.png'>

  会撑大原来的盒子

- 解决：

  通过给设置了宽高的盒子，减去相应的内边距的值，维持盒子原有的大小

  <img src='./media/32padding问题解决.png'>




### **课堂一练**

1. 一个盒子宽度为100， padding为 10， 边框为5像素，问这个盒子实际的宽度的是（）

- [x] (A) 130
- [ ] (B) 135 
- [ ] (C) 125
- [ ] (D) 115



100 +  20 + 10 



2. 关于根据下列代码计算 盒子宽高下列说法正确的是（）

```css
div {

		width: 200px;

        height: 200px;

		border: 1px solid #000000;

		border-top: 5px solid blue;

		padding: 50px;

		padding-left: 100px;

		}
```

- [ ] (A) 宽度为200px 高度为200px
- [x] (B) 宽度为352px 高度为306px
- [ ] (C) 宽度为302px 高度为307px
- [ ] (D) 宽度为302px 高度为252px

w：  200 +   150   + 2   =  352

h ：  200 +  100 +  6   =  306 

### 6) padding不影响盒子大小情况

> 如果没有给一个盒子指定宽度， 此时，如果给这个盒子指定padding， 则不会撑开盒子。

## 5. 外边距（margin）

<img src='./media/18margin外边距.png'>

### 1) 外边距

​	margin属性用于设置外边距。  margin就是控制**盒子和盒子之间的距离**

### 2) 设置：

| 属性            | 作用   |
| ------------- | :--- |
| margin-left   | 左外边距 |
| margin-right  | 右外边距 |
| margin-top    | 上外边距 |
| margin-bottom | 下外边距 |

margin值的简写 （复合写法）代表意思  跟 padding 完全相同。

### 3) 块级盒子水平居中

- 可以让一个块级盒子实现水平居中必须：
  - 盒子必须指定了宽度（width）
  - 然后就给**左右的外边距都设置为auto**，

实际工作中常用这种方式进行网页布局，示例代码如下：

~~~css
.header{ width:960px; margin:0 auto;}
~~~

常见的写法，以下下三种都可以。

* margin-left: auto;   margin-right: auto;
* margin: auto;
* margin: 0 auto;

### 4) 文字居中和盒子居中区别

1.  盒子内的文字水平居中是  text-align: center,  而且还可以让 行内元素和行内块居中对齐
2.  块级盒子水平居中  左右margin 改为 auto 

~~~css
text-align: center; /*  文字 行内元素 行内块元素水平居中 */
margin: 10px auto;  /* 块级盒子水平居中  左右margin 改为 auto 就阔以了 上下margin都可以 */
~~~

### 5) 插入图片和背景图片区别

1. 插入图片 我们用的最多 比如产品展示类  移动位置只能靠盒模型 padding margin
2. 背景图片我们一般用于小图标背景 或者 超大背景图片  背景图片 只能通过  background-position

~~~css
 img {  
		width: 200px;/* 插入图片更改大小 width 和 height */
		height: 210px;
		margin-top: 30px;  /* 插入图片更改位置 可以用margin 或padding  盒模型 */
		margin-left: 50px; /* 插入当图片也是一个盒子 */
	}

 div {
		width: 400px;
		height: 400px;
		border: 1px solid purple;
		background: #fff url(images/sun.jpg) no-repeat;
		background-position: 30px 50px; /* 背景图片更改位置 我用 background-position */
	}
~~~

### 6) 清除元素的默认内外边距(重要)

<img src='./media/19清除内外边距.png'>

为了更灵活方便地控制网页中的元素，制作网页时，我们需要将元素的默认内外边距清除

代码： 

~~~css
* {
   padding:0;         /* 清除内边距 */
   margin:0;          /* 清除外边距 */
}
~~~

注意：  

* 行内元素为了照顾兼容性， 尽量只设置左右内外边距， 不要设置上下内外边距。

### 7) 外边距合并

使用margin定义块元素的**垂直外边距**时，可能会出现外边距的合并。

#### a. 相邻块元素垂直外边距的合并

- 当上下相邻的两个块元素相遇时，如果上面的元素有下外边距margin-bottom
- 下面的元素有上外边距margin-top，则他们之间的垂直间距不是margin-bottom与margin-top之和
- **取两个值中的较大者**这种现象被称为相邻块元素垂直外边距的合并（也称外边距塌陷）。

 <img src="media/www.png" />

**解决方案：尽量给只给一个盒子添加margin值**。

#### b. 嵌套块元素垂直外边距的合并（塌陷）

- 对于两个嵌套关系的块元素，如果父元素没有上内边距及边框
- 父元素的上外边距会与子元素的上外边距发生合并
- 合并后的外边距为两者中的较大者

 <img src="media/n.png" />

**解决方案：**

1. 可以为父元素定义上边框。
2. 可以为父元素定义上内边距
3. 可以为父元素添加overflow:hidden。

还有其他方法，比如浮动、固定、绝对定位的盒子不会有问题，后面咱们再总结。。。

## 6. 盒子模型布局稳定性

- 学习完盒子模型，内边距和外边距，什么情况下用内边距，什么情况下用外边距？

  - 大部分情况下是可以混用的。  就是说，你用内边距也可以，用外边距也可以。 你觉得哪个方便，就用哪个。

我们根据稳定性来分，建议如下：

按照 优先使用  宽度 （width）  其次 使用内边距（padding）    再次  外边距（margin）。   

```
  width >  padding  >   margin   
```

- 原因：
  - margin 会有外边距合并 还有 ie6下面margin 加倍的bug（讨厌）所以最后使用。
  - padding  会影响盒子大小， 需要进行加减计算（麻烦） 其次使用。
  - width   没有问题（嗨皮）我们经常使用宽度剩余法 高度剩余法来做。


