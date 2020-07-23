# 什么是HTML？
* HTML指 __H__ yper __T__ ext __M__ akeup __L__ anguage
* 不是一种编程语言，是一种标记语言
* 标记语言是一套标记标签
* HTML文档也叫做web页面

## HTML标签
* 标签是由<>包围的关键词，比如`<html>`
* 通常成对出现，但是有例外

## HTML元素
通常来说，元素与标签是一个意思，但是严格来讲，元素包含了开始标签与结束标签。

## 书写格式
首先要声明HTML版本`<!DOCTYPE HTML>`(对html不区分大小写，HTML也可以写成html)

然后如果使用了中文，为了避免乱码，需要在头部将字符声明为UTF-8或GBK

__另外,title是必须的！__

~~~HTML
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>emm</title>
</head>
~~~

# HTML元素举例
## 连接外部样式表 \<link>

~~~html 
<head>
<link rel="stylesheet" type="text/css" href="css文件地址">
</head>
~~~

除了引入样式表，还可以显示网页logo

~~~HTML
<head>
<link rel="shortcut icon" href="图片url">
</head>
~~~

## 标题 \<h1>-\<h6>

标题是通过\<h1>-\<h6>标签来定义的
~~~html
<h1>title</h1>
<h6>titlr</h6>
~~~

## 段落 \<p>\</p>

由于p标签中是不会自动换行的，因此如果要实现换行，可以使用\<br>标签。
~~~html
<p>this is <br>a<br>grap
~~~

## 链接 \<a href="">text</a>

超链接可以是一个字，一个词，或者一组词，也可以是一幅图像，您可以点击这些内容来跳转到新的文档或者当前文档中的某个部分。

text不一定是文本，也可以是其它的html元素，比如图片等

使用target属性，可以决定链接文档在何处打开，下面例子将在新窗口打开
~~~html
<a href="" target="_blank" ></a>
~~~

可以使用id来连接到文档内的部分

~~~html
<a href="#example">go to comment</a>
<p id="example">this is a comment</p>
~~~

## 图像 \<img src="" width="" height="" alt="" />
>提示: 指定图像的高度和宽度是一个很好的习惯。如果图像指定了高度宽度，页面加载时就会保留指定的尺寸。如果没有指定图片的大小，加载页面时有可能会破坏HTML页面的整体布局。
### 配合img使用的map、area标签
使得在同一张图片中，能有多处不同的点击地点
~~~html
<!DOCTYPE html>
<html>
	<body>
		<h1>my web </h1>
		<p id="demo">a pra</p>
		<img src="img.jpg" width="100" height="100" alt="nono" usemap="#platmap">
		<map name="platmap">
			<area shape="circle" coords="10,10,10" href="" alt="">
			<area shape="rect" coords="50,50,60,60" href=""alt="">
		</map>

	</body>
</html>
~~~

href可能有三个值：
* 指向站点内某个文件 href="img.jpg"
* 指向另一个站点 href="http://112.124.31.67"
* 指向另一个元素 href="#name"

## 文本格式化标签

|标签|描述|
|---|---|
|\<b>|定义粗体文本|
|\<em>|定义着重文字|
|\<i>|定义斜体字|
|\<small>|定义小号字|
|\<strong>|定义加重语气|
|\<sub>|定义下标字|
|\<sup>|定义上标字|
|\<ins>|定义插入字|
|\<del>|定义删除字|

## 计算机输出标签

|标签|描述|
|---|---|
|\<code>|定义计算机代码|
|\<kbd>|定义键盘码|
|\<samp>|定义计算机代码样本|
|\<var>|定义变量|
|\<pre>|定义预格式文本|

## HTML 引文, 引用, 及标签定义

|标签|描述|
|---|---|
|\<abbr>|定义缩写|
|\<address>|定义地址|
|\<bdo>|定义文字方向|
|\<blockquote>|定义长的引用|
|\<q>|定义短的引用语|
|\<cite>|定义引用、引证|
|\<dfn>|定义一个定义项目|

## HTML的列表
### 无序
~~~html
<ul>
	<li>1</li>
	<li>2</li>
</ul>
~~~

### 有序
~~~html
<ol>
<li>Coffee</li>
<li>Milk</li>
</ol>
~~~

## HTML的区块

大多数的HTML元素被定义为 __块级元素__ 或 __内联元素__
块级元素在浏览器显示时，会*自动换行*

而 __内联函数__ 通常不会以新行开始。

### \<div>元素与\<span>元素

前者是区块元素，没有什么特定含义，用来组合其它元素，类似一个容器
后者是内联元素，也没有什么特定含义，可以作为文本容器，与CSS一起使用时，可以为部分文本设计专属的样式。

## HTMl表单
表单是一个包含表单元素的区域。

表单元素是允许用户在表单中输入内容,比如：文本域(textarea)、下拉列表、单选框(radio-buttons)、复选框(checkboxes)等等。

表单使用表单标签 \<form> 来设置:
~~~html
<form>
...
...
...
</form>
~~~

### 输入元素\<input>
#### 文本域
~~~HTML
<form>
First name: <input type="text" name="firstname"><br>
Last name: <input type="text" name="lastname">
</form>
~~~

#### 密码字段
~~~html
<form>
Password: <input type="password" name="pwd">
</form>
~~~

#### 单选按钮(radio buttons)
~~~HTML
<form>
<input type="radio" name="sex" value="male">Male<br>
<input type="radio" name="sex" value="female">Female
</form>
~~~

#### 复选框(Checkboxes)
~~~html
<form>
<input type="checkbox" name="vehicle" value="Bike">I have a bike<br>
<input type="checkbox" name="vehicle" value="Car">I have a car
</form>
~~~

#### 提交按钮(Submit Button)
~~~html
<form name="input" action="html_form_action.php" method="get">
Username: <input type="text" name="user">
<input type="submit" value="Submit">
</form>
~~~

#### 一个不限输入的文本框
~~~html
<textarea rows="10" cols="30">
我是一个文本框。
</textarea>
~~~


# HTML属性
每个元素都可以有它自己的属性，属性是HTML元素提供的附加信息，常以键值对的形式出现，如`class = "First"`

常见属性：
* class 为元素定义一个*或多个*类名
* id 为元素定义唯一id
* style 规定元素的行内样式(inline style)
* title 描述了元素的额外信息




