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
