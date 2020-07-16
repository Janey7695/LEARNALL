学习教程来源：[JavaScript教程]("https://www.runoob.com/js")
---

# 1.JavaScript 用法
## 1.1 `<script>标签`
在一个HTML页面中使用JS，需要加入`<script>`标签。`<script>`和`</script>`之间的代码包含了JS内容。
~~~html
<script>
alert("my first JavaScript");
</script>
~~~

## 1.2 在`<head>或者<body>`中的JS
可以在HTML文档中放入无限的脚本，脚本可以位于<body>或者<head>部分中，也可以同时存在2个部分中。通常的做法是全部放入<head>中，或者全部放在页面底部。
### 1.2.1 在`<body>`中使用JS
~~~html
<!DOCTYPE html>
<html>
	<body>
		<script>
			document.write("<h1>this is a title</h1>");
		</script>
	</body>
</html>
~~~
JS会在加载页面时执行`<script>`标签内的代码。

### 1.2.2 在`<head>` 中使用JS

~~~html
<!DOCTYPE html>
<html>
	<head>
		<script>
			function myFunction()//定义一个可调用函数
			{
				document.getElementById("demo").innerHTML="my first JS";
			}
		</script>
	</head>
	<body>
		<script>
			document.write("<h1>this is a title</h1>");
		</script>
		<h1>my web </h1>
		<p id="demo">a pra</p>
		<button type="button" onclick="myFunction()">TRY</button>
	</body>
</html>
~~~

## 1.3 外部的JS
可以在外部写JS文件，后缀是.js，并且在<script>标签中声明src来源（类似头文件）
`<script src="myScript.js"></script>`
外部脚本编写时无需包含<script>标签，与css类似。

# 2.JavaScript输出
## 2.1 JS显示数据
首先，JS没有类似c语言的打印或者输出函数，它通过其它方式来输出数据
* 使用window.alert()弹出警告框
* 使用document.write()将内容写入到HTML文档中
* 使用innerHTML写入到HTML元素
* 使用console.log()写入到浏览器的控制台

### 2.1.1 `window.alert()`
~~~html
<!DOCTYPE html>
<html>
    <body>

        <h1>我的第一个页面</h1>
        <p>我的第一个段落。</p>

        <script>
            window.alert(5 + 6);
        </script>

    </body>
</html>
~~~

### 2.1.2 操作HTML元素
使用document.getElementById(id)来寻找相应的id，并使用innerHTML来获取或插入元素内容
~~~html
<!DOCTYPE html>
<html>
    <body>

        <h1>我的第一个 Web 页面</h1>

        <p id="demo">我的第一个段落</p>

        <script>
            document.getElementById("demo").innerHTML = "段落已修改。";
        </script>

    </body>
</html>
~~~
运行结果是<p id="demo">中的内容被修改了 。

### 2.1.3 `document.wirte()`
需要注意的是，如果在文档加载后才执行`document.write()`,整个HTML页面会被覆盖。具体看下面2个例子的对比：
~~~html
examlpe1
<!DOCTYPE html>
<html>
	<body>
		<h1>my web </h1>
		<script>
			document.write(Date());
		</script>
		<p id="demo">a pra</p>
	</body>
</html>
~~~
再看第二个例子：
~~~html
example2
<!DOCTYPE html>
<html>
	<body>
		<h1>my web </h1>
		<script>
			function myFunction(){
				document.write(Date());
			}
		</script>
		<p id="demo">a pra</p>
		<button type="button" onclick="myFunction()">HIT</button>
	</body>
</html>
~~~

对比2个例子的效果，可以发现第一个例子的Date被正常的写入在正确的位置，而第二个例子当按下按钮时，页面先被清空，然后才显示Date。

### 2.1.4 写到控制台
使用`console.log()`写东西到控制台里面
~~~html
<!DOCTYPE html>
<html>
    <body>

        <h1>我的第一个 Web 页面</h1>

        <script>
            a = 5;
            b = 6;
            c = a + b;
            console.log(c);
        </script>

    </body>
</html>
~~~
按F12打开调试窗口，可以在 控制台 里面看到输入了一个 11 。
