# CSS3 边框属性
>border-radius:

使用这个属性来快速创建边框圆角
~~~css
div
{
border:2px solid;
border-radius:25px;
}
~~~

*它也可以分别指定四个角的radius大小，规则跟margin一样*

>box-shadow:

使用这个属性来添加阴影
~~~css
div
{
box-shadow: 10px 10px 5px #888888;
}
~~~

语法：`box-shadow: h-shadow v-shadow blur spread color inset;`

|值|说明|
|---|---|
|h-shadow|必需的。水平阴影的位置。允许负值|
|v-shadow|必需的。垂直阴影的位置。允许负值|
|blur|可选。模糊距离|
|spread|可选。阴影的大小|
|color|可选。阴影的颜色。在CSS颜色值寻找颜色值的完整列表|
|insert|可选。从外层的阴影（开始时）改变阴影内侧阴影|

>border-image:[1](https://www.runoob.com/cssref/css3-pr-border-image.html)

# CSS3 背景
提供额外的背景处理属性

>background-image:
~~~css
#example1 { 
    background-image: url(img_flwr.gif); 
    background-position: right bottom, left top; 
    background-repeat: no-repeat, repeat; 
}
~~~

# CSS3 渐变Gradients
2种渐变：线性渐变和径向渐变
>linear-gradient线性渐变

语法：`background-image: linear-gradient(direction, color-stop1, color-stop2, ...);`

1. 从上到下(默认):
   ~~~css
   #grad {
    background-image: linear-gradient(#e66465, #9198e5);
   }
   ~~~
2. 从左到右：
  ~~~css
  #grad {
  height: 200px;
  background-image: linear-gradient(to right, red , yellow);
  }
  ~~~
3. 对角：
  ~~~css
  #grad {
  height: 200px;
  background-image: linear-gradient(to bottom right, red, yellow);
  }
  ~~~
4. 使用角度：
  ~~~CSS
  #grad {
  background-image: linear-gradient(-90deg, red, yellow);
  }
  ~~~
5. 使用透明度：
   使用rbga表示颜色，即可改变透明度
   ~~~css
   #grad {
    background-image: linear-gradient(to right, rgba(255,0,0,0), rgba(255,0,0,1));
    }
   ~~~
6. 重复线性渐变`background-image: repeating-linear-gradient(red, yellow 10%, green 20%);`

>radial-gradient 径向渐变

语法：`background-image: radial-gradient(shape size at position, start-color, ..., last-color);`

# CSS3 Transform

## 1.2D转换

>transform:translate();

translate()方法，根据左(X轴)和顶部(Y轴)位置给定的参数，从当前元素位置移动。
~~~css
div
{
transform: translate(50px,100px);
}
~~~

>transform:rotate();

rotate()方法，在一个给定度数顺时针旋转的元素。负值是允许的，这样是元素逆时针旋转。
~~~css
div
{
transform: rotate(30deg);
}
~~~

>transform:scale()

scale()方法，该元素增加或减少的大小，取决于宽度（X轴）和高度（Y轴）的参数：
~~~css
transform: scale(2,3);
~~~
上例中，宽度变为原来的2倍，高度变为原来的3倍

>transform:skew();

语法：`transform:skew(<angle> [,<angle>]);`
包含两个参数值，分别表示X轴和Y轴倾斜的角度，如果第二个参数为空，则默认为0，参数为负表示向相反方向倾斜。
~~~css
div
{
transform: skew(30deg,20deg);
-ms-transform: skew(30deg,20deg); /* IE 9 */
-webkit-transform: skew(30deg,20deg); /* Safari and Chrome */
}
~~~
skew(30deg,20deg) 元素在X轴和Y轴上倾斜20度30度。

>transform:matrix();

## 2.3D变换

>rotateX()和rotateY()

# CSS3 过渡 transition
CSS3 过渡是元素从一种样式逐渐改变为另一种的效果。

要实现这一点，必须规定两项内容：
* 指定要添加效果的CSS属性
* 指定效果的时间

指定的CSS属性的 __值更改时__ 效果会发生变化。一个典型CSS属性的变化是用户鼠标放在一个元素上时:
~~~html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8"> 
<style> 
div
{
	width:100px;
	height:100px;
	background:red;
	transition:width 2s;
	-webkit-transition:width 2s; /* Safari */
}

div:hover
{
	width:300px;
}
</style>
</head>
<body>

<p><b>注意：</b>该实例无法在 Internet Explorer 9 及更早 IE 版本上工作。</p>

<div></div>

<p>鼠标移动到 div 元素上，查看过渡效果。</p>

</body>
</html>
~~~

可以通过添加由逗号隔开的多个样式变化效果
~~~css
div
{
    transition: width 2s, height 2s, transform 2s;
    -webkit-transition: width 2s, height 2s, -webkit-transform 2s;
}
~~~

# CSS3 动画 animation

