# 3. JS语法
## 3.1 JS的 __字面量__
有固定值的东西称为字面量，个人感觉用法其实类似python，如：
1. 数字(number)字面量：可以是整数，小数，或者科学计数，如：`3.14`，`1001`，`2e5`
2. 字符串(string)字面量：可以用单引号或者双引号：`"hello"`,`'hello'`
3. 表达式字面量：用于计算：`5+6`，`3*9`
4. 数组(array)字面量：`[40,1,3,5,2,7]`
5. 对象(object)字面量：类似字典？ `{firstName:"John", lastName:"Doe", age:50, eyeColor:"blue"}`
6. 函数(function)字面量：定义一个函数：`function myFunction(a, b) { return a * b;}`

## 3.2 JS的 __变量__
JS中一律使用`var`来定义变量，用=进行赋值，即`var`创建一个变量，`=`赋了什么值决定它的数据类型，这点也类似python
当执行`16+"hello"`时，结果会是`16hello`。

## 3.3 JS的字符集
JS使用Unicode字符集，攘括了所有字符，包括标点符号。

## 3.4 其它
* JavaScript会忽略多余的空格，可以适当添加空格增加可读性。
* 可以在文本字符串中使用反斜杠对代码进行换行
  eg.document.write("你好 \
  世界!");
* 编码风格比较正常，结尾要;风格与c语言类似，语法与python类似。
* 注释方式，与c语言相同 使用//或者/*...*/

# 4. JavaScript的 数据类型
值类型：字符串，数字，布尔，对空(NULL)，未定义，symbol(表示独一无二的值)
引用数据类型：对象,数组，函数

JS拥有动态类型，意味着同一个变量可以有不同类型的值，如
~~~js
var x;
x=5;//现在x为数字
x="hello"//现在x为字符串
~~~

## 4.1 数组
有多种创建方式：
~~~js
var cars=new Array();
cars[0]="Saab";
cars[1]="Vovol";
~~~
或者
~~~js
var cars=new Array("Saab","Vovol");
~~~
或者
~~~js
var cars=["Saab","Vovol"];
~~~

## 4.2 对象
创建方法：`var person={firstname:"John", lastname:"Doe", id:5566};`
对象属性有2中寻址方法：
* `name=person.lastname;`
* `name=person["lastname"];`

## 4.3 NULL和Undefine
Undefine表示这个变量不含有值
可以通过设置一个变量的值为NULL来清空变量(释放内存)

## 4.3 指定变量类型
由于JS拥有动态类型，其实变量是什么数据类型不重要，但是如果有时候需要用到某种特定的数据类型，可以通过new的方式来声明变量：
~~~js
var x = new String;
var y = new Number;
var z = new Boolean;
var h = new Array;
var k = new Object;
~~~
__需要注意的是，JS变量均为对象！因此当你声明一个变量时，就创建了一个对象__

