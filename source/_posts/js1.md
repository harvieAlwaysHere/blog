---
title: JS（一）基本语法
tags:
  - JavaScript
  - Web前端
date: 2018-02-25 11:28:57
categories: ItEassy
---
### JavaScript 是一种轻量级的脚本编程语言，是可插入 HTML 页面的编程代码，几乎可由所有的现代浏览器执行。
![](/images/js.jpg)

JavaScript能做什么？
{% codeblock %}
//1.直接写入 HTML输出流
document.write("<h1>这是一个标题</h1>");

//2.对事件的反应函数
<button type="button" onclick="alert('欢迎!')">点我!</button>

//3.改变HTML内容样式（DOM (Document Object Model)（文档对象模型））
<script>
function myFunction()
{
	x=document.getElementById("demo");  // 找到元素
	x.innerHTML="Hello JavaScript!";    // 改变内容
	x.style.color="#ff0000";          // 改变样式
}
</script>

//4.改变HTML图像
<script>
function changeImage()
{
	element=document.getElementById('myimage')
	if (element.src.match("bulbon"))
 	{
  		element.src="/images/pic_bulboff.gif";
  	}
	else
   {
  		element.src="/images/pic_bulbon.gif";
   }
}
</script>

//5.验证输入
<script>
function myFunction()
{
	var x=document.getElementById("demo").value;
	if(x==""||isNaN(x))
	{
		alert("不是数字");
	}
}
</script>
{% endcodeblock %}

### JS用法
JS需要被放置在**< script>标签**内
< script>标签可位于**<head>或者<body>标签**内
我们可以**直接在< script>标签内编写JS代码**或者**使用< script src="xxx.js"></script>引用外部JS代码**
Tips：有些老旧的实例可能会在< script>便签中使用**type="text/javascript"**，现在已经不必了，因为JS已经是所有现代浏览器和HTML5中默认的脚本语言了

### JS输出
有不同方式输出，大致有四种
1.使用**window.alert()**弹出警告栏
2.使用**document.write()**将内容写入HTML文档中(页面加载前)
3.使用**x.innerHTML**写入x元素的HTML内容
4.使用**console.log()**写入浏览器的控制台
Tips：console.log()用于调试JS是十分方便的，因为它可以**显示结构化的数据**，如Array、Object可以看到数组和对象的内容，而且**不会打断页面操作**，页面可以继续执行接下来的操作

### JS语法
**JS字面量**：固定值，如
* **数字(Number)**：整数小数科学计数，314,3.14,123e5**
* **字符串(String)**：单引号双引号字符，'Harvie',"Harvie"
* **表达式字面量**：带运算符，5+6，5*6
* **数组(Array)**：[40,50,60]
* **对象(Object)**：{firstName:"Harvie",lastName:"Ryan",age:50}
* **函数(function)**：function myfunction(a,b){return a*b}

**JS变量**：存储数据值，使用关键字**var**声明变量，使用**=**为变量赋值

**JS操作符**：分为**赋值/算数/位运算符(=,+,-,*,/)**和**条件/比较/逻辑运算符(==,!=,<,>)**

**JS语句**：向浏览器发出命令，用**;(分号)**分隔

**JS注释**：不是命令的语句，用**//(双斜杠)**分隔

**JS数据类型**：数字、字符串、数组、对象等，使用相应的字面量赋值，是**弱类型的**，都是通过var声明

**JS大小写和字符集**：JS对于**大小写敏感**，使用**Unicode字符集**

### JS语句
JavaScript语句通常以一个**语句标识符**为开始，并执行该语句，如下。
![](/images/js11.png)

### JS注释
//...单行注释，/*...*/多行注释，...//行末注释

### JS变量
变量是用于存储信息的"容器"，可以使用短名称(x,y)，也可以使用描述性名称(age,num),但是要符合一下规则
![](/images/js12.png)
Tips：如果重新声明 JavaScript 变量，该变量的值不会丢失，如var x=1;var x;，x的值仍为1
Tips：**let变量**，**作用域被限制在块级**中的变量、语句或者表达式，在Function中局部变量使用let变量可避免变量名冲突，**var变量作用域是整个封闭函数**
{% codeblock %}
function varTest() {
  var x = 1;
  if (true) {
    var x = 2;  // 同样的变量  
    console.log(x); //2 
  }
  console.log(x);   // 2
}

function letTest() {
  let x = 1;
  if (true) {
    let x = 2;  // 不同的变量    
    console.log(x);  // 2  
  }
  console.log(x);    // 1
}
{% endcodeblock %}

### JS数据类型
![](/images/js13.png)
JS拥有**动态类型**，声明的变量类型均为**弱类型**，可随意切换，如
{% codeblock %}
var x; x=1; x="HARVIE"; x=[1,2,3];
{% endcodeblock %}
JS**字符串**中使用单引号和双引号需要使用**\+引号**，如
{% codeblock %}
var x="His name is \"Harvie\"";
{% endcodeblock %}
JS只有一种**数字**类型，可以带小数点，可以使用科学计数(e)，如
{% codeblock %}
var x=314; x=3.14; x=314e-2;
{% endcodeblock %}
JS**布尔**类型只有两个值，true或者false
{% codeblock %}
var x=true; var y=false;
{% endcodeblock %}

JS**对象和数组**：详情见[博文JS（二）](harviealwayshere.github.io/2018/02/26/js2/)
JS**空类型**
**Undefined**表示变量**不含有值**，如变量刚声明时为Undefined
**Null**表示变量为空，可通过**将变量的值设为null**来**清空变量**

### JS函数
由事件驱动的或者当它被调用时执行的可重复使用的，使用关键词**function**，包裹在**{ }**中的代码块

### JS作用域
**局部作用域**：变量在函数内声明，变量为局部作用域，只能在函数内部访问
**全局作用域**：变量在函数外声明即为全局变量，若**变量在函数内没有声明(没有使用var关键字)**，该变量自动升级为**全局变量**
**HTML中的全局变量**：在HTML中，<font color='red'>所有全局变量都属于window对象</font>
，都可以使用**window.变量名**访问
**let关键字**变量：可将变量作用域限制在当前代码块中
**const关键字**变量：所声明的是常量，其值不可以更改

### JS字符串
用于存储一系列字符，用**单引号或双引号**包裹
可以在字符串中**添加转义字符**来使用引号等符号，如var x = 'It\'s alright';
![](/images/js16.png)
可以使用内置属性**length**来计算字符串长度，如x.length;更多字符串方法可以看[这里](http://www.runoob.com/jsref/jsref-obj-string.html)

### JS条件语句
![](/images/js17.png)

### JS循环语句
![](/images/js18.png)
介绍一下**For/In循环**，主要用来遍历对象的属性(name+value)，如
{% codeblock %}
var Person={firstName:"John", lastName:"Doe", id:5566};
var txt="";
for (x in Person){
  txt=txt+x+"=>"+Person[x]+"\n";
}
{% endcodeblock %}

### JS typeof操作符
**typeof**操作符：用于检测变量的数据类型，如typeof x，返回x的数据类型
**null**：JS中一个只有一个值的数据类型，表示一个**空对象的引用**，typeof null为object，可以将**对象**设为null来**清空对象**
**undefined**：JS中一个没有设置值的变量，typeof undefined为undefined，可以将**任何变量**设置为undefined来**清空变量**
Tips：**instanceof**用于**判断对象具体类型**，如var result = objectName instanceof objectType，若objectName属于ObjectType类型则返回true，否则返回false

### JS类型转换
JS有5种数据类型：string/number/boolean/object/function
3种对象类型：Object/Date/Array
2个不包含任何值的类型：null/undefined
Tips:NaN指示该值不是数字值，某些**算数运算**(如求负数平方根)结果或者**类型转换**结果(parseInt()/parseFloat())，可用**isNaN()**判断一个值是否是NaN值
Tips:数组Array/日期Date/null的数据类型均是object

**变量的constructor属性**：**返回所有JS变量的构造函数**，可用来判断对象是否为数组、日期，如
{% codeblock %}
function isArray(myArray) {
    return myArray.constructor.toString().indexOf("Array") > -1;
}
function isDate(myDate) {
    return myDate.constructor.toString().indexOf("Date") > -1;
}
{% endcodeblock %}

**JS变量的类型转换**：有2种方法，1.通过使用**JS函数**，2.通过JS的自身**自动转换**
**JS函数：**
全局方法**String()**可将变量转为字符串，如Srting(123)/String(false)/String(new Date())
全局方法**Number()**可将变量转为数字，如Number("3.14")/Number("99 88")=>返回NaN/Number(new Date())
全局方法**parseFloat()/parseInt()**将字符串转为一个浮点数/整数
**自动转换:**
**一元运算符+**：自动统一表达式两边变量类型转换成同一数据类型
**.innerHTML**：输出一个对象或者变量时，JS会自动调用该变量的toString()方法，将改变量转换成String

### JS正则表达式
正则表达式是使用字符串来描述、匹配一系列符合某个句法规则的字符串搜索模式，可用于文本搜索和文本替换
JS正则语法格式：**/正则表达式主体/修饰符(可选)**
修饰符：i=>对大小不敏感，g=>全局匹配，m=>多行匹配
**字符串方法**使用正则：
1.**serach()**：检索字符串中指定的子字符串，若匹配则返回子串的起始位置
2.**replace()**：用于在字符串中用一些字符替换另一些字符
**RegExp对象**使用正则：
1.**test()**方法用于检测一个字符串是否匹配某个模式，若字符串中含有匹配的文本，则返回true，否则返回false，如
{% codeblock %}
/*是否带有小数*/
function    isDecimal(strValue )  {  
   var  objRegExp= /^\d+\.\d+$/;
   return  objRegExp.test(strValue);  
}  

/*校验是否中文名称组成 */
function ischina(str) {
    var reg=/^[\u4E00-\u9FA5]{2,4}$/;   /*定义验证表达式*/
    return reg.test(str);     /*进行验证*/
}

/*校验是否全由8位数字组成 */
function isStudentNo(str) {
    var reg=/^[0-9]{8}$/;   /*定义验证表达式*/
    return reg.test(str);     /*进行验证*/
}

/*校验电话码格式 */
function isTelCode(str) {
    var reg= /^((0\d{2,3}-\d{7,8})|(1[3584]\d{9}))$/;
    return reg.test(str);
}

/*校验邮件地址是否合法 */
function IsEmail(str) {
    var reg=/^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/;
    return reg.test(str);
}
{% endcodeblock %}
2.**exec()**方法用于检索字符串中正则表达式的匹配，若找到匹配的结果则返回一个数组存放，若未找到则返回null

### JS错误
**try**语句测试代码块的错误
**catch**语句处理错误（系统发现的错误）
**throw**语句创建或抛出异常(自己发现的错误)
语法：
{% codeblock %}
try {
  //在这里运行代码
  if(x==y) throw "error!";
} catch(err) {
  //在这里处理错误，如
  alert(err.message);
}
{% endcodeblock %}

### JS调试
1.**console.log()方法**：在浏览器控制台打印关键信息
2.**debugger关键字**：设置**断点**调试，如var x=1;debugger;var y=1;

### JS变量提升(Hoisting)
JS中，**函数及变量的声明**都将被提升到函数的最顶部，因此变量可以在**使用后声明**。但是只有声明的变量会提升，**初始化的变量不会提升**

### JS严格模式(Strict Mode)
使用命令**"use strict";**进入严格模式，可以**消除Javascript语法的一些不合理、不严谨之处，减少一些怪异行为**，1.增加代码安全性，2.提高编译器效率，3.为未来新版本的JS做好铺垫

### JS表单验证
1.表单事件验证：
可以利用表单的**onsubmit事件**，在表单提交时被触发，执行验证函数，验证表单数据，若为**return false**则可阻止表单提交
获取表单的数据可用**document.forms["formName"]["valueName"].value;**获取表单数据
如**非空验证**
{% codeblock %}
<form name="myForm" action="demo_form.php"
onsubmit="return validateForm()" method="post">
名字: <input type="text" name="fname">
<input type="submit" value="提交">
</form>
<script>
function validateForm() {
    var x = document.forms["myForm"]["fname"].value;
    if (x == null || x == "") {
        alert("需要输入名字。");
        return false;
    }
}
</script>
{% endcodeblock %}
如**E-mail验证**(必须包含@符号和点号(.)/@不可以是邮件地址的首字符/@后至少一个点号)
{% codeblock %}
<script>
function validateForm(){
  var x=document.forms["myForm"]["email"].value;
  var atpos=x.indexOf("@");
  var dotpos=x.lastIndexOf(".");
  if (atpos<1 || dotpos<atpos+2 || dotpos+2>=x.length){
    alert("不是一个有效的 e-mail 地址");
    return false;
  }
}
</script>
{% endcodeblock %}

2.HTML约束验证：
可在HTML元素中直接增加约束，如required="required"/disabled/pattern/max/min/type等详情可见[这里](http://www.runoob.com/html/html5-form-attributes.html)

3.服务器端数据验证：数据提交到服务器后再验证


### JS JSON
JSON全称**JavaScript Object Notation**，轻量级**存储和传输数据的格式**，通常用于**服务端向网页传递数据**。
JSON格式：
1.数据为**键值对("key":"value")**，由**逗号**分隔，如"name":"Runoob"
2.**大括号{}**保存**对象**，{"name":"Runoob", "url":"www.runoob.com"}
3.**方括号[]**保存**数组**，数组中可保存对象，如

**JSON字符串转换为JS对象**：我们从服务器中接收JSON数据并利用**JSON.parse(text[, reviver])**将JSON字符串转换为JS对象，**reviver**为转换结果函数将会调用对象的每个成员，如**function(k,v)**
{% codeblock %}
var text = '{ "sites" : [' +
	'{ "name":"Runoob" , "url":"www.runoob.com" },' +
	'{ "name":"Google" , "url":"www.google.com" },' +
	'{ "name":"Taobao" , "url":"www.taobao.com" } ]}';
JSObj = JSON.parse(text);
// JSObj.sites[1].name => Google
// JSObj.sites[1].url => www.google.com
{% endcodeblock %}


**JS对象转换为JSON字符串**：我们可以使用**JSON.stringify(value[, replacer[, space]])**方法将JS对象转换为JSON字符串传递给服务器，**space**可为文本添加缩进、空格、换行符，如int i则添加i个空格缩进，若为\t则添加制表符
{% codeblock %}
var str = {"name":"菜鸟教程", "site":"http://www.runoob.com"};
str_pretty1 = JSON.stringify(str); //{"name":"菜鸟教程","site":"http://www.runoob.com"}
{% endcodeblock %}


### JS死链接
我们经常看到**href="javascript:void(0)"**，表示点击这个链接是不会发生任何跳转的链接
void()方法表示**不返回任何值**，void()中的语句**会执行但是返回的结果不会被void传递到函数外部**

**href="#"**表示定位到**#位置**，#包含了一个**位置信息**，默认的锚**#top**也就是网页的上端，可用**#+id**定位到页面的具体位置

### JS函数
**自调用函数**：**(function(){})();**表示这是一个自调用函数，运行至脚本处会自动触发调用
**全局对象**：若函数没有被自身的对象调用时，**this的值会变成全局对象**，web浏览器中全局对象是浏览器窗口(**window对象**)
**对象方法调用函数**：函数可作为对象的方法被调用，如var x = {y:function(){ return 0;}}，可通过x.y()调用函数
**构造函数调用函数**：函数调用使用**new关键字**则调用了构造函数，创建了新的**对象**，如function y(){this.z=1;};var x= new y();，则x为一个对象{"z" : 1}


### JS闭包
计数器困境：**若计数器为全局变量**，则除了add()计数函数外其他所有函数也可以访问计数器变量，不安全；**若计数器变量在add()计数函数内声明并初始化为0**，则每次调用add()函数就都会初始化变量失去了计数功能
闭包：**父函数内嵌函数会持有父方法的局部变量并且不会随父方法销毁而销毁**，就是说即便父函数已经执行完了但是里面的变量被赋给了别的变量，这个变量并不会随着父函数的调用结束而消失
{% codeblock %}
<script>
var add = (function () {
    var counter = 0;
    return function () {return counter += 1;}
})();
</script>
//利用自调用函数，执行一次申明并初始化变量counter的过程
之后返回一个匿名函数给add，这样add就变成了一个函数add(){return counter +=1;}
这个函数持有父函数的变量counter但是即便父函数执行结束后counter并没有被销毁
因此每次执行add()，都会使counter++，也仅有执行add()才能调用到counter变量，保证了变量的私有性
{% endcodeblock %}
