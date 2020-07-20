# 5. JS的对象
现实生活中一辆车是一个对象，对象有它的属性，如颜色、重量、大小等，有它的方法，如启动方式，熄火方式等，JS中的对象也是这样。

在JS中，几乎所有事物都是对象，对象也可以是一个变量，但是对象可以包含多个值(多个变量).
~~~js
var car = {type:"Fiat",model:500,color:"white"};
~~~
上面的例子中，三个变量赋予了car，三个值也依次赋予了car。

__JavaScript*对象*是*变量*的容器__

访问对象有2种方式，前文已经提到了。

对象方法：如果在对象内定义了一个函数，这个函数就是这个对象的方法，使用时要加上()：`name = person.fullName();`
如果没有加上()，则会返回函数的定义，如下：
~~~html
<!DOCTYPE html>
<html>
    <body>

        <p>创建和使用对象方法。</p>
        <p>对象方法是一个函数定义,并作为一个属性值存储。</p>
        <p id="demo1"></p>
        <p id="demo2"></p>
        <script>
            var person = {
                firstName: "John",
                lastName : "Doe",
                id : 5566,
                fullName : function() 
                {
                return this.firstName + " " + this.lastName;
                }
            };
            document.getElementById("demo1").innerHTML = "不加括号输出函数表达式："  + person.fullName;
            document.getElementById("demo2").innerHTML = "加括号输出函数执行结果："  +  person.fullName();
        </script>
        
    </body>
</html>
~~~
使用以下语法创建一个对象方法：
~~~js
var car = {
    color:"white";
    weight:100;
    start:function(){
        //代码
    }
}
~~~

# 6. JS的作用域
JavaScript 变量生命周期在它声明时初始化。
局部变量在函数执行完毕后销毁。
全局变量在网页关闭后销毁

# 7.JS的事件
HTML事件是发生在HTML元素上的事情。当在HTML上使用JS时JS可以触发这些事件。
>常见的HTML事件
* onchange HTML元素改变
* onclick 用户点击HTML元素
* onmouseover 用户在一个HTML元素上移动鼠标
* onmouseout 用户从一个HTML元素上移开鼠标
* onkeydowm 用户按下键盘按键
* onload 浏览器完成页面的加载
* ...

通常，当事件发生时，你可以通过JS代码来执行一些事情，HTML元素中可以添加事件属性，使用JS代码来添加HTMl元素。
例子：
~~~html
<button onclick="getElementById('demo').innerHTML=Date()">现在的时间</button>
~~~

上面的代码修改id=demo里面的内容，下面的例子修改元素本身
~~~html
<button onclick="this.innerHTML=Date()">现在的时间是？</button>
~~~

# 8. JS的字符串方法
|方法|描述|
|---|---|
|charAt()|返回指定索引位置的字符|
|charCodeAt()|返回指定索引位置字符的 Unicode 值|
|concat()|	连接两个或多个字符串，返回连接后的字符串|
|fromCharCode()|将 Unicode 转换为字符串|
|indexOf()|	返回字符串中检索指定字符第一次出现的位置|
|lastIndexOf()|返回字符串中检索指定字符最后一次出现的位置|
|localeCompare()|用本地特定的顺序来比较两个字符串|
|match()|找到一个或多个正则表达式的匹配|
|replace()|替换与正则表达式匹配的子串|
|search()|检索与正则表达式相匹配的值|
|slice()|提取字符串的片断，并在新的字符串中返回被提取的部分|
|split()|把字符串分割为子字符串数组|
|substr()|从起始索引号提取字符串中指定数目的字符|
|substring()|提取字符串中两个指定的索引号之间的字符|
|toLocaleLowerCase()|根据主机的语言环境把字符串转换为小写，只有几种语言（如土耳其语）具有地方特有的大小写映射|
|toLocaleUpperCase()|根据主机的语言环境把字符串转换为大写，只有几种语言（如土耳其语）具有地方特有的大小写映射|
|toLowerCase()|把字符串转换为小写|
|toString|返回字符串对象值|
|toUpperCase()|把字符串转换为大写|
|trim()|移除字符串首尾空白|
|valueOf()|返回某个字符串对象的原始值|

# 9. JS的正则表达式
正则表达式(Regular Expression,简写为regex,regexp或RE)。正则表达式是由一个字符序列形成的搜索模式。当在文本中搜索数据时，可以用搜索模式来描述我要查询的内容。正则表达式可以是一个简单的字符，或则一个更复杂的模式。

__语法__
`/表达式主体/(修饰符(可选))`

表达式主体用于检索，修饰符不区分大小写
>修饰符的作用

|修饰符|描述|
|---|---|
|i|执行对大小写不敏感的匹配|
|g|执行全局匹配(查找所有匹配而非在找到第一个匹配后停止)|
|m|执行多行匹配|

__常见的方法__
1. search()方法
使用str.search(/hello/i)来搜索

~~~html
<!DOCTYPE html>
<html>
    <body>

        <p>搜索字符串 "hello", 并显示匹配的起始位置：</p>
        <button onclick="myFunction()">点我</button>
        <p id="demo"></p>
        <script>
            function myFunction() {
                var str = "ohhhhh hello";
                var n = str.search(/hello/i);
                document.getElementById('demo').innerHTML=n;
            }
        </script>
    </body>
</html>
~~~

2. replace()方法
使用replace(/hello,"nohello")来替换文本

~~~html
<!DOCTYPE html>
<html>
    <body>
        <p>替换 "nohello" 为 "hello" :</p>
        <button onclick="myFunction()">点我</button>
        <p id="demo">there is nohello</p>
        <script>
            function myFunction() {
                var str = document.getElementById('demo').innerHTML;
                var txt = str.replace(/nohello/i,"hello");
                document.getElementById('demo').innerHTML=txt;
            }
        </script>
    </body>
</html>
~~~

几个正则表达式对象方法：
3. test()方法
用于检测一个字符串是否匹配某个模式，如果字符串中含有匹配的文本，则返回true，否则返回false。

~~~js
var patt = /e/;
patt.test("the best things in life are free!");
~~~
返回true

4. exec()方法
exec() 方法用于检索字符串中的正则表达式的匹配。
该函数返回一个数组，其中存放匹配的结果。如果未找到匹配，则返回值为 null
~~~js
var patt = /e/;
patt.exec("the best things in life are free!");
~~~
返回e

*2020/7/20 15：42*