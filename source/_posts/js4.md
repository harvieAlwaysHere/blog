---
title: JS（四）JS常用对象
tags:
  - JavaScript
  - Web前端
date: 2018-02-27 15:21:19
categories: ItEassy
---
### JavaScript 中的所有事物都是对象：字符串、数值、数组、函数...
![](/images/js.jpg)

### JS对象基本概念
JS对象由**花括号分隔**，**对象属性**以**名称和值对的形式(name:value)**来定义，**对象属性**由**逗号分隔**
JS提供多个内建对象，如String、Date、Array等，对象是一种特殊的数据类型拥有**属性和方法**
**对象属性**：是与对象有关的**值**，可用**object.att或者object["att"]**访问，如var message="hello world";var length=message.length;
**对象方法**：是能够在对象上执行的**动作**，可用**objectName.methodName()**访问，如var message="hello world";var upperMsg=message.toUpperCase();

### JS对象创建
JS创建对象有种方式
1. **字面量创建**使用**{ }**包围
{% codeblock %}
var Person={firstName:"John", lastName:"Doe", id:5566};
{% endcodeblock %}
2. **Object创建**
{% codeblock %}
var Person=new Object();
Person.firstName:"John";
Person.lastName:"Doe";
Person.id:5566;
{% endcodeblock %}
3. **工厂模式创建**
{% codeblock %}
function createPerson(firstName,lastName,id)
{
  var obj=new Object();
  obj.firstName=firstName;
  obj.lastName=lastName;
  obj.id=id;
  return obj;
}
var Person1=createPerson("John","Doe",5566);
var Person2=createPerson("Arvin","Harvie",7788);
{% endcodeblock %}
4. **构造函数创建**
{% codeblock %}
function Person(firstName,lastName,id)
{
  this.firstName=firstName;
  this.lastName=lastName;
  this.id=id;
}
var Person1=new Person("John","Doe",5566);
var Person2=new Person("Arvin","Harvie",7788);
{% endcodeblock %}
5. **原型创建对象**
{% codeblock %}
function Person(){}
Person.prototype.firstName="John";
Person.prototype.lastName="Doe";
Person.prototype.id=5566;
var person1=new Person();
var person2=new Person();
person2.firstName="Arvin";
person2.lastName="Harvie";
person2.id=7788;
{% endcodeblock %}
6. **组合构造函数和原型创建对象**
{% codeblock %}
function Person(firstName,lastName,id){
 this.firstName =firstName;
 this.lastName = lastName;
 this.id = id;
}
Person.prototype = {
 constructor:Person,
 showId: function(){
 alert(this.id);
 };
}
{% endcodeblock %}
### 总结：
第1/2种字面量和Object构建对象，对于**简单属性**的对象而言十分**简单明了**，但是<font color='red'>它们都是用了同一个接口创建很多对象，会产生大量的重复代码，就是如果你有100个对象，那你要输入100次很多相同的代码</font>
第3种**工厂模式**创建对象，将**创建对象过程封装在函数体内**，通过函数调用生成对象，但是<font color='red'>无论哪个对象的生成函数返回的都是一个对象，我们无法判断返回的对象是哪个类型</font>
第4种**构造函数**创建对象，没有显式创造对象，没有return语句，而且**可以识别对象类型**，使用**instanceof**操作符进行检测(person insanceof Person);但是<font color='red'>若对象中含有方法，则每个方法都要在实例中创建一遍，然而每个对象的方法应该是相同的才对，若是方法的数量很多，就会占用很多不必要的内存</font>
第5中**原型**创建对象，可以让**所有对象实例共享它所包含的属性和方法**，若为对象实例添加一个属性，这个属性会**覆盖**原型对象中的属性，<font color='red'>但是这样子每个对象实例的属性一般都是不同的，创建起来就很复杂</font>
第6中**原型和构造函数结合**创建对象，是最好的，**构造函数用于定义实例属性**使每个对象实例有自己的属性，**原型模式用于定义方法和共享的属性**使每个对象相同的方法和属性共用同一片内存，节省空间

### JS遍历对象
可以使用**for...in...循环**遍历对象属性，语法：
for (name in object)
{
  text=text+name+"=>"+object[name]+"\n";
}

### JS Number对象
JS中的数字可以使用小数点，也可以不使用小数点，如var x=314;var y=3.14;
JS中数字均为**浮点型**，采用IEEE754标准定义的**64位**浮点格式表示
JS中**八进制**前缀为**0**，**十六进制**前缀为**0x**，可以使用**Number.toString(8/16/2)转换进制**
JS中的数字运算**超出了数字上限**，则以**Infinity**表示
JS中的数字运算**发生错误产生了非数字值**，则以**NaN**表示，可以用**isNaN()
Number**对象属性**：
1. object.constructor：返回创建此对象的函数引用
2. Number.MAX_VALUE：返回Number可表示的最大的数
3. Number.MIN_VALUE：返回Number可表示的最小的数
4. Number.NaN：返回NaN

Number**对象方法**：
1. NumberObject.toString(radix=10)：将Number对象转换为**radix进制的字符串**
2. NumberObject.toLocaleString()：将Number对象转换为**本地格式**的字符串
3. NumberObject.toFixed(num=0)：将Number四舍五入为**num(0-20)小数位数的数字**
4. NumberObject.toExponential(num)：将Number转换成**num(0-20)小数位的指数计数**
5. NumberObject.valueOf()：返回Number对象的值的**字符串**

### JS String对象
String**对象属性**：
1. object.constructor：返回创建此对象的函数引用
2. stringObject.length：返回字符串中字符数目

String**对象方法**：
1. stringObject.indexOf(searchvalue,fromindex)：返回searchvalue在stringObject中首次出现的位置，fromindex为开始检索的位置(可选)，方法**大小写敏感**且**无检索结果返回-1**
2. stringObject.match(searchvalue/regexp)：返回stringObject中**匹配searchvalue的字符串**或者**匹配regexp正则表达式的字符串**，若regexp具有**标志g**则为**多次匹配**返回所有匹配字符串的**数组**
3. stringObject.replace(regexp/substr,replacement)：返回用replacement替换了regexp/substr匹配的字符串的新字符串
4. stringObject.toLocaleUpperCase()/toLocaleLowerCase()：返回大写/小写字符串
5. stringObject.split(separator,howmany)：返回以separator字符串或正则表达式分割的howmany个数的字符串数组，若用""分割则每个字符都会分割
6. 更多对象方法可查看[这里](http://www.w3school.com.cn/jsref/jsref_obj_string.asp)

### JS Date对象
**new Date();**会自动把**当前日期和时间**保存为其初始值，可调用对象方法更改Date对象属性
Date**对象方法**：
1. dateObject.getFullYear()/getMonth()/getDate()/getDay()：返回年份/月份(0-11)/日期/星期(0-6)
2. dateObject.getHours()/getMinutes()/getSeconds()：返回小时(0-23)/分钟(0-59)/秒数(0-59)
3. dateObject.getTime()：返回距 1970 年 1 月 1 日之间的毫秒数
4. dateObject.setFullYear(year,month,day)/setMonth(month,day)/setDate(day)：设置年份/月份/日期
5. dateObject.toUTCString()/toString()/toDateString()/toTimeString()：将日期转换为UTC世界时/本地时间/日期部分本地时间/时间部分本地时间的字符串

在页面上显示时钟(Date+onload事件)
{% codeblock %}
<script>
function startTime(){
	var today=new Date();
	var h=today.getHours();
	var m=today.getMinutes();
	var s=today.getSeconds();// 在小于10的数字前加一个‘0’
	m=checkTime(m);
	s=checkTime(s);
	document.getElementById('block').innerHTML=h+":"+m+":"+s;
	t=setTimeout(function(){startTime()},500);
}
function checkTime(i){
	if (i<10){
		i="0" + i;
	}
	return i;
}
</script>
<body onload="startTime()">
	
<div id="block"></div>
{% endcodeblock %}

### JS Array对象
JS**数组**可以有三种声明赋值方式
1. 创建数组再一一赋值:
{% codeblock %}
var cars=new Array();
cars[0]="Saab";
cars[1]="Volvo";
cars[2]="BMW";
{% endcodeblock %}
2. 创建时就赋值(Condensed Array):使用**( )**包围
{% codeblock %}
var cars=new Array("Saab","Volvo","BMW");
{% endcodeblock %}
3. 不创建直接赋值(Literal Array):使用**[ ]**包围
{% codeblock %}
var cars=["Saab","Volvo","BMW"];
{% endcodeblock %}

##### 总结：
1/2方式在于先创建一个空的多维数组，之后可使用**for循环根据需求赋值**，3对于**枚举数据**比较简单易用
由于JS的**弱检查特性**，因此在**JS数组中可以同时存储不同类型的变量**，比如你可以把数字、字符串、字符、对象等内容放在同一个数组中。

Array**对象属性**：
1. arrayObject.length：返回数组中元素数目

Array**对象方法**：
1. arrayObject.concat(arrayX,arrayX,......,arrayX)：连接两个或多个数组，不会改变现有数组而是产生一个新的连接结果数组
2. arrayObject.join(separator)：把arrayObject中所有元素放入一个字符串以separator分割，默认以，分割
3. arrayObject.pop()：删除数组最后一个元素并返回元素的值，若数组为空则返回undefined
4. arrayObject.push(newelement1,newelement2,....,newelementX)：向数组末尾增加一个或多个元素并返回新的数组长度
5. arrayObject.reverse()：将arrayObject数组元素颠倒顺序，会改变原数组
6. arrayObject.shift()：删除并返回第一个数组元素并返回元素的值，若为空则返回undefined
7. arrayObject.slice(start,end)：返回arrayObject数组中下标从start到end的子数组，不改变原数组，可用负值表示从尾部选取元素
8. arrayObject.sort(sortby)：以sortby函数对数组排序，若无排序函数则以字符编码的顺序进行排序，排序函数有两个参数ab，若a在b前则返回小于0，a在b后则返回大于0
9. arrayObject.splice(index,howmany,item1,.....,itemX)：若howmany=0，则向数组中从index开始添加item1,.....,itemX个元素，若howmany>0，则会删除从index开始后howmany个元素并以item1,.....,itemX代替，返回删除的元素数组，会改变原数组
10. arrayObject.toString()：返回，分割数组元素的字符串
11. arrayObject.unshift(newelement1,newelement2,....,newelementX)：向数组开头添加一个或多个元素并返回新的长度

### JS Math对象
Math对象**无需创建可静态使用**
Math**对象属性**：
Math.E(自然底数)/.LN2/.LN10(2/10的自然对数)/.LOG2E/.LOG10E(以2/10为底的e的对数)/.PI(圆周率)/.SQRT1_2/.SQRT2(返回2的平方根的倒数/返回2的平方根)
Math**对象方法**：
几乎所有的算数运算都有，详情见[这里](http://www.w3school.com.cn/jsref/jsref_obj_math.asp)
Math.round(x)：返回x最接近的整数
Math.random()：返回0.0-1.0之间的伪随机数

### JS RegExp对象
RegExp 对象表示正则表达式，**直接量语法**：**/pattern/attributes**，**创建RegExp对象**的语法：**new RegExp(pattern, attributes);**
* **参数pattern**是一个**字符串**，指定了**正则表达式的模式**或其他正则表达式。
* **参数attributes**是一个**可选的字符串**，包含属性 **"g"、"i" 和 "m"**，分别用于指定**全局匹配、区分大小写的匹配和多行匹配**。
Tips：**pattern是正则表达式**，而不是字符串，则**必须省略该参数attributes**。
具体使用可查阅[这里](http://www.w3school.com.cn/jsref/jsref_obj_regexp.asp)

