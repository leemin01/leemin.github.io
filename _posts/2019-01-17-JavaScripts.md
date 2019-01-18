---
layout: post
title: JavaScript
date: 2019-1-14
categories: blog
tags: [javaStudy]
description: JavaScript。

---

* 目录
{:toc}

1.  JavaScript

JavaScript概述
==============

什么是JS
--------

JS全称JavaScript， 简称JS

JS是一门基于对象和事件驱动的脚本语言，专门为网页交互而设计，主要应用在客户端（浏览器）

(1)基于对象: JS不能称之为面向对象, 因为JS不具备面向对象语言的特征.
JS没有编译的过程,直接执行的就是源文件(源代码), JS也没有类的概念,
也不是通过类产生对象.

(2)事件驱动: 在JS中, 很多时候都是通过事件触发来驱动函数执行,
从而实现某些功能.

(3)脚本语言: 在网络前端开发环境下, 能够嵌入在浏览器中执行的一段小程序.

JS的特点和优势
--------------

### JS的特点

解释执行, 不需要编译(直接执行的就是源代码)

是一门基于对象的语言, 不是面向对象.

弱类型

在Java语言中

```java
String str = "Hello CGB1807";

int num = 100;

boolean b = true;
```

在JS中:

```js
var str = "Hello CGB1807";

var num = 100;

var b = true;

b = 100;

b = "Hello";
```

### JS的优势

交互性

安全性

跨平台性

如何在html中引入JS
------------------

### 通过script标签内部书写JS代码

引入JS的第 1 种方式

```html
<script>

alert("引入JS的第一种方式...");

console.log("引入JS的第一种方式...");

</script>
```

### 通过script标签引入外部的JS文件

引入JS的第 2 种方式, 在HTML中:


```html
<script src="demo.js"></script>

在demo.js中:

alert("引入JS的第二种方式...");

console.log("引入JS的第二种方式...");
```

JavaScript语法
==============

数据类型
--------

### 基本数据类型

JS中的基本数据类型一共有5种

1、数值类型

在JS种，所有的数值在底层都是浮点型。

但是，在处理或者显示的时候，JS会自动的在整型和浮点型之间进行转换。

比如：2.4+3.6=6;//结果是6, 不是6.0

NaN (not a number): 表示非数字, NaN和任何值都不相等, 包括它本身.
如果要判断一个值是否是NaN, 不能用 == 来判断,
可以使用isNaN函数来进行判断.

比如: "123", 可以转成数值, 所以不是非数字

isNaN( "123" );//false

比如: "abc123", 不可以转成数值, 所以是非数字

isNaN( "abc123" );//true

2、字符串类型

在JS中, 字符串类型属于基本数据类型.

字符串常量可以用双引号或者单引号引起来.

```js
var str1 = "Hello CGB1807";

var str2 = 'Hello CGB1807';
```

3、布尔类型

布尔类型的值分为true和false.

4、undefined

undefined, 表示变量未定义, 值只有一个就的undefined

如果声明了一个变量, 但是没有为变量赋值, 变量的值则为undefined, 例如:

var x;

console.log(x);//undefined

注意: undefined不是说变量没有声明, 而是声明了变量但是没有赋值,
如果变量没有声明直接访问, 会抛出异常!

5、null

null类型的值只有一个, 就是null

null表示空值, 可以作为函数的返回值, 表示函数返回的是一个空的对象.

### 复杂数据类型

JS中的复杂数据类型其实就的对象

对象(数组、函数、对象)

变量和运算符
------------

### 变量

在JS中是通过var关键字来声明变量

变量本身不区分数据类型,可以指向任意的数据类型

### 运算符

JS中的运算符和Java中的运算符大致相同

算术运算符: +，-，*，/，%，++，--

赋值运算符: =，+=，-=，=，/=，%=

比较运算符: ==，!=，===，!==，>，>=，<，<=

位运算符: & ， |

逻辑运算符: && ，||

前置逻辑运算符: ! (not)

三元运算符: ? :

其他运算符: typeof

1、==和===的区别

==和===的区别

```js
console.log( 123 == (100+23) );//true

console.log( 123 === (100+23) );//true

console.log( "123" == (100+23) );//true

console.log( "123" === (100+23) );//false
```

在比较两个值是否相等时, 如果两个值属于同一种数据类型,
==和===在比较时没有区别.

如果比较的两个值不是同一种数据类型, ===直接返回false, 不予比较,
==会先将两个值转成同一种数据类型,
再进行比较.如果相等则返回true,否则返回false.

2、typeof运算符 -- 返回变量或者表达式的数据类型

```js
var x = 100;

console.log( typeof x );//number

x = true;

console.log( typeof x );//boolean

x = "hello";

console.log( typeof x );//string
```

语句
----

JS中的语句和Java中的大致相同

### if...else

和Java不同的是, JS中的语句的判断条件如果不是布尔类型, 会先转成布尔类型,
再进行判断, 例如:

```js
if( 123 ){// "123"

console.log("yes");

}else{

console.log("no");

}
```

除了数值中的0和NaN以及字符串中的""会转成false，
其他的数值和字符串都是true

### 循环语句

示例:1.需求：声明一个数组，来存储0\~100之间的偶数

```js
var arr = [];//声明一个空数组

var index = 0;

for(var i=0;i<100;i++){

if(i % 2 == 0){

arr[index] = i;

index++;

}

}

console.log(arr);
```

数组
----

### 声明方式一

声明一个空数组

var arr1 = new Array();

声明一个具有指定初始值的数组

var arr2 = new Array(88, "Hello", true, 100);

### 声明方式二

声明一个空数组

var arr1 = [];

声明一个具有指定初始值的数组

var arr2 = [88, "Hello", true, 100];

### 数组中的属性及方法

1.length属性 -- 返回数组的长度

2.pop方法 -- 删除数组中的最后一个元素并返回该元素

3.push方法 -- 往数组中的最后一个位置添加一个元素并返回新数组的长度

4.shift方法 -- 删除数组中的第一个元素并返回该元素

5.unshift方法 -- 往数组中的第一个位置添加一个元素并返回新数组的长度

函数
----

JS中的函数作用就是Java中的方法：其实就是具有功能的代码块，可以反复调用！

### 声明方式一

声明函数：

function 函数名称([参数列表]){

函数体

...

}

调用函数：

函数名称([参数列表]);

示例函数声明方式一:

```js
function fn(name, age){

alert(name+":"+age);

}

fn("王海涛", 18);
```

### 声明方式二

声明函数：

var 变量名/函数名称 = function ([参数列表]){

函数体

...

}

调用函数：

函数名称([参数列表]);

示例函数声明方式二:

```js
var fn2 = function(name, nickname){

alert(name+":"+nickname);

}

fn2("刘沛霞", "皮皮霞");
```

需要注意的是： JS中的函数在调用时，
即使少传参数，或者多传参数都可以调用！

对象
----

### 自定义对象

方式一：

声明一个对象

```js
function Person(){}

var p1 = new Person();
```

为对象动态添加属性和方法

```js
p1.name = "齐雷";

p1.age = 19;

p1.run = function(){

alert(this.name+":"+this.age);

}
```

访问对象上的属性和方法

```js
alert(p1.name);

alert(p1.age);

p1.run();
```

方式二：

声明一个对象p2

```js
var p2 = {

"name" : "刘昱江",

"age" : "17",

"run" : function(){

alert(this.name+":"+this.age);

}

}
```

访问对象上的属性或方法

```js
alert(p2.name);

alert(p2.age);

p2.run();
```

### JS中的内置对象

1、String对象

声明String对象

```js
var str1 = new String("Hello");

var str2 = "Hello";//基本数据类型，需要时会转成对象
```

(1)length属性 -- 返回字符串的长度

```js
var str = "Hello";

console.log(str.length);//5

(2)indexOf/lastIndexOf方法

var str1 = "Hello JavaScript java";

console.log(

str1.indexOf("Java")

);//6
```

(3)match方法

-- 根据正则表达式到字符串中进行匹配，将所有匹配的结果以数组的形式返回

```js
var str1 = "Hello JavaScript java";

var = /Java/ig;

alert(

str1.match( reg1 )

);//Java,java
```

(4)substr方法

-- 截取字符串, 从start开始, 截取指定的长度


```js
var str1 = "Hello JavaScript java";

console.log(

str1.substr(6,10)

);//JavaScript
```

(5)slice方法

-- 截取字符串, 从start开始, 到end结束

var str1 = "Hello JavaScript java";

console.log(

str1.slice(6,16)

);//JavaScript

2、RegExp对象

声明正则对象方式一:

var reg1 = /Java/;

声明正则对象方式二:

var reg2 = new RegExp("Java");

test方法:

```js
var email = "admin123\@163.com";//true

email = "admin123\@sina.com.cn";//true

email = "@@\@admin123\@sina.com.cn@\#\$\#@\$\#@%\$\#";//false

var reg1 = /\^\\w+@\\w+(\\.\\w+)+\$/;

reg1 = new RegExp("\^\\\\w+@\\\\w+(\\\\.\\\\w+)+\$");//作用同上

console.log(

reg1.test( email )

);
```

3、Date对象

声明日期对象1：

var date1 = new Date();//获取表示当前时间的Date对象

声明日期对象2：

var date2 = new Date(123134121000);//1970-1-1

声明日期对象3：

var date3 = new Date(2018, 7, 30);

常用的方法：

(1)getFullYear() -- 获取年份

(2)getMonth() -- 获取月份

(3)getDate() -- 获取天数

(4)getHours() -- 获取小时

(5)getMinutes() -- 获取分钟

(6)getSeconds() -- 获取秒值

示例：

```js
var date1 = new Date(2018, 7, 30, 13, 35, 48);

console.log(

date1.toLocaleString()

);//2018/8/30 下午1:35:48

console.log(

date1.getFullYear()

);//2018

console.log(

date1.getMonth()

);//7(表示8月)

console.log(

date1.getDate()

);//30(一月中的第几天)

console.log(

date1.getDay()

);//4(表示星期四)

console.log(

date1.getHours()

);//13(h)

console.log(

date1.getMinutes()

);//35(m)

console.log(

date1.getSeconds()

);//48(s)
```

4、Math对象

(1)Math.PI -- 圆周率

(2)Math.ceil() -- 向上取整

Math.ceil(123.45) -- 124

(3)Math.round() -- 四舍五入

Math.round(123.45) -- 123

(4)Math.floor() -- 向下取整

Math.floor(123.45) -- 123

5、Global对象

Global对象是一个全局对象，在调用其方法时，直接调用即可，不需要加对象点

(1)parseInt() -- 将传入的值转成整数

(2)parseFloat() -- 将传入的值转成浮点数

(3)isNaN() -- 判断传入的值是否是非数字

(4)eval() -- 将传入的字符串按照JS的语法解释执行

示例:

```js
console.log(

parseInt( "123.45" )

);//123

console.log(

parseFloat( "123.45" )

);//123.45

console.log(

isNaN( "123.45" )

);//false

console.log(

isNaN( "abc123" )

);//true

eval( "alert(3+4)" );
```
