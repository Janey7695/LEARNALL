# CSS的组成
一般都由一个选择器，以及一条或多条声明构成的语法结构

选择器是我们想要修改的元素

声明总是以;结束，需要用{}包括：`p {color:red;text-align:center;}`

CSS注释：/* ..... */

# id和class选择器
id用 . 
class 用 #

# CSS Background 
用于定义html元素的背景
CSS 背景的效果：
* background-color
* background-image
* background-repeat
* background-attachment
* background-position

## 1.background-color
定义了元素的背景颜色
`body {background-color:#b0c4de;}`

## 2.background-image
background-image 属性描述了元素的背景图像.

默认情况下，背景图像进行平铺重复显示，以覆盖整个元素实体
`body {background-image:url('img.jpg');}`

## 3.background-repeat
设置图片的平铺方式
`body {background-image:url('gradient2.png');background-repeat:repeat-x;}`设置为水平平铺

`body {background-image:url('gradient2.png');background-repeat:no-repeat;}`设置为不平铺

`body {background-image:url('img_tree.png');background-repeat:no-repeat;background-position:right top;}`设置图片放置方位

## 4.使用简写background
如果同时需要用到很多背景属性，就可以用background进行简写：
~~~html
body {background:#ffffff url('img_tree.png') no-repeat right top;}
~~~

声明顺序为：
* background-color
* background-image
* background-repeat
* background-attachment
* background-position

以上属性无需全部使用，需要什么用什么即可

***注意background-color与color的区别***:前者用于背景，后者用于文字！

# CSS链接
~~~css
a:link {color:#000000;}      /* 未访问链接*/
a:visited {color:#00FF00;}  /* 已访问链接 */
a:hover {color:#FF00FF;}  /* 鼠标移动到链接上 */
a:active {color:#0000FF;}  /* 鼠标点击时 */
~~~
使用text-decration 可以消除链接的下划线
~~~css
a:link {text-decoration:none;}
a:visited {text-decoration:none;}
a:hover {text-decoration:underline;}
a:active {text-decoration:underline;}
~~~

# Box Model！ 
所有的HTML元素可以看作盒子，CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。

盒模型允许我们在其它元素和周围元素边框之间的空间放置元素。

[img](https://www.runoob.com/images/box-model.gif)

不同部分说明：
1. Margin(外边距)-清除边框外的区域，外边距是透明的
2. Border(边框) - 围绕在内边距和内容外的边框
3. Padding(内边距) - 清除内容周围的区域，内边距是透明的。
4. Content(内容) - 盒子的内容，显示文本和图像。

## 1.Border(边框)的属性

>border-style:
* none:默认无边框
* dotted: 定义一个点线边框
* dashed: 定义一个虚线边框
* solid: 定义实线边框
* double: 定义两个边框。 两个边框的宽度和 border-width 的值相同
* groove: 定义3D沟槽边框。效果取决于边框的颜色值
* ridge: 定义3D脊边框。效果取决于边框的颜色值
* inset:定义一个3D的嵌入边框。效果取决于边框的颜色值
* outset: 定义一个3D突出边框。 效果取决于边框的颜色值

>border-width:

为边框指定宽度有两种方法：可以指定长度值，比如 2px 或 0.1em(单位为 px, pt, cm, em 等)，或者使用 3 个关键字之一，它们分别是 thick 、medium（默认值） 和 thin。

>border-color:

设置颜色，可以用三种方法来表示颜色
1. name-比如red、black...
2. RGB-指定指定 RGB 值, 如 rgb(255,0,0)
3. Hex - 指定16进制值, 如 #ff0000
4. 边框颜色还可以设置成transparent(透明)
5. border-color单独使用是不起作用的，必须得先使用border-style来设置边框样式。

>边框各边单独设置
~~~css
p
{
    border-top-style:dotted;
    border-right-style:solid;
    border-bottom-style:dotted;
    border-left-style:solid;
}
~~~

>使用border简写：

上面的例子用了很多属性来设置边框。
你也可以在一个属性中设置边框。
你可以在"border"属性按如下顺序中设置：
* border-width
* border-style (required)
* border-color
~~~css
border:5px solid red;
~~~

## 2.Margin(外边距)属性
margin 清除周围的（外边框）元素区域。margin 没有背景颜色，是完全透明的。

margin 可以单独改变元素的上，下，左，右边距，也可以一次改变所有的属性。
>单边外边距：

~~~css
margin-top:100px;
margin-bottom:100px;
margin-right:50px;
margin-left:50px;
~~~
>margin简写：

~~~css
margin:100px 50px;
~~~

margin可以有一到四个值：
* margin:25px 50px 75px 100px;四个方向
* margin:25px 50px 75px; 上 左右 下
* margin:25px 50px;上下 左右
* margin:25px;上下左右

## 3.Padding(内边距) 同上margin

# Position(定位)
position 属性指定了元素的定位类型，主要有五个值：
* static
* relative
* fixed
* absolute
* sticky

## 1.static
HTML 元素的默认值，即没有定位，遵循正常的文档流对象。

静态定位的元素不会受到 top, bottom, left, right影响。

## 2.fixed
元素的位置相对于浏览器窗口是固定位置。

即使窗口是滚动的它也不会移动：
~~~css
p.pos_fixed
{
    position:fixed;
    top:30px;
    right:5px;
}
~~~

## 3.relative
相对定位元素的定位是相对其正常位置,移动相对定位元素，但它原本所占的空间不会改变。

## 4.absolute
绝对定位的元素的位置相对于最近的已定位父元素，如果元素没有已定位的父元素，那么它的位置相对于<html>
absolute 定位使元素的位置与文档流无关，因此不占据空间。

absolute 定位的元素和其他元素重叠。
## 5.sticky
sticky 英文字面意思是粘，粘贴，所以可以把它称之为粘性定位。

position: sticky; 基于用户的滚动位置来定位。

粘性定位的元素是依赖于用户的滚动，在 position:relative 与 position:fixed 定位之间切换。

它的行为就像 position:relative; 而当页面滚动超出目标区域时，它的表现就像 position:fixed;，它会固定在目标位置。

元素定位表现为在跨越特定阈值前为相对定位，之后为固定定位。

这个特定阈值指的是 top, right, bottom 或 left 之一，换言之，指定 top, right, bottom 或 left 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。
~~~html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<style>
div.sticky {
  position: -webkit-sticky;
  position: sticky;
  top: 0;
  padding: 5px;
  background-color: #cae8ca;
  border: 2px solid #4CAF50;
}
</style>
</head>
<body>

<p>尝试滚动页面。</p>
<p>注意: IE/Edge 15 及更早 IE 版本不支持 sticky 属性。</p>

<div class="sticky">我是粘性定位!</div>

<div style="padding-bottom:2000px">
  <p>滚动我</p>
  <p>来回滚动我</p>
  <p>滚动我</p>
  <p>来回滚动我</p>
  <p>滚动我</p>
  <p>来回滚动我</p>
</div>

</body>
</html>
~~~

>z-index：

使用它来确定元素最后显示的堆叠顺序，具有更高堆叠顺序的元素总是在较低的堆叠顺序元素的前面。

# Overflow属性
CSS overflow 属性可以控制内容溢出元素框时在对应的元素区间内添加滚动条。

|值|描述|
|---|---|
|visible|默认值。内容不会被修剪，会呈现在元素框之外。|
|hidden|内容会被修剪，并且其余内容是不可见的|
|scroll|内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容|
|auto|如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。|
|inherit|规定应该从父元素继承 overflow 属性的值。|

# float属性
CSS 的 Float（浮动），会使元素向左或向右移动，其周围的元素也会重新排列。

Float（浮动），往往是用于图像，但它在布局时一样非常有用。

# CSS的伪类与伪元素
>anchor伪类 ： 前面的 css链接 中已经说过了

>:first-child 
匹配任何元素的第一个\<p>元素

>更多伪类：[1](https://www.runoob.com/css/css-pseudo-classes.html)

>伪元素：[2](https://www.runoob.com/css/css-pseudo-elements.html)