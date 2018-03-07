---
title: C++基础语法
tags:
  - C++
date: 2018-01-31 15:31:56
categories: ItEassy
---
### 刚想刷剑指offer和leetcode上的题目，本来享用python写的，毕竟是刷算法感觉无论是什么语言总是相通的，但看到剑指offer上写到
![](/images/cplusplus.png)
### 大一学的C++基础语法都忘记的差不多了，还是先来巩固一下C++基础语法，本文以runoob和C++ Primer(第五版)为基础编写。
<br>
## Part1：C++基础语法
#### 标准的 C++ 由三个重要部分组成：
1. **核心语言**，提供了所有构件块，包括变量、数据类型和常量，等等。
2. **C++ 标准库**，提供了大量的函数，用于操作文件、字符串等。
3. **标准模板库（STL）**，提供了大量的方法，用于操作数据结构等。

#### C++面向对象四大特点:
封装、抽象、继承和多态。

#### C++基本程序结构：
{% codeblock %}
#include <iostream>         //添加头文件
using namespace std;        //使用std命名空间
 
int main()                  //主函数，程序执行入口
{
   cout << "Hello World";   // 输出 Hello World
   return 0;                //终止main函数并返回0值
}
{% endcodeblock %}

#### C++内置数据类型：
类型&emsp;&emsp;&emsp;&emsp;关键字&emsp;&emsp;&emsp;&emsp;内存(字节)
布尔型	&emsp;&emsp;&emsp;bool&emsp;&emsp;&emsp;&emsp;&emsp;1
字符型	&emsp;&emsp;&emsp;char&emsp;&emsp;&emsp;&emsp;&emsp;1
整型		&emsp;&emsp;&emsp;&emsp;int&emsp;&emsp;&emsp;&emsp;&emsp;&nbsp;&nbsp;&nbsp;4
浮点型	&emsp;&emsp;&emsp;float&emsp;&emsp;&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;4
双浮点型	&emsp;&emsp;double&emsp;&emsp;&emsp;&emsp;8
无类型	&emsp;&emsp;&emsp;void&emsp;&emsp;&emsp;&emsp;&emsp;
宽字符型	&emsp;&emsp;wchar_t&emsp;&emsp;&emsp;&nbsp;&nbsp;4
一些类型可用**修饰符**修饰:
**signed,unsigned,short,long.**
![](/images/leixing.png)
![](/images/weishu.png)
Tip:其实知道了字节数很简单计算范围，1字节=8位，每位存储一个二进制0/1，有几位二进制则可以存储2^位数范围大小的数字。
Tip:存储**有符号数**的时候，例如int，虽然是4字节的，但是只能是1符号位+31数值为位，所以最大为2^31=2147483647。
<br>
可以使用**typedef**为一个已有的类型取一个新的名字:typedef type newname; 
{% codeblock %}
typedef int SSS;
SSS a = 2;
{% endcodeblock %}
#### 枚举类型(enumeration)：
是C++中的一种派生数据类型，它是由用户定义的若干枚举常量的集合:
语法为:enum name {IDentifier0[=int 0],IDentifier1=[int 1]} Variable;
{% codeblock %}
enum color { red, green, blue } c;    //定义变量c是枚举类型color
c = blue;                             //c赋值为blue
{% endcodeblock %}
#### 变量声明和定义:
您可以使用**extern关键字**在任何地方**声明**一个变量。虽然您可以在 C++ 程序中多次声明一个变量，但变量只能在某个文件、函数或代码块中被**定义一次**。
{% codeblock %}
// 变量声明
extern int a;      //可进行多次
int main ()
{
  // 变量定义       //仅可进行一次
  int a;
 
  // 实际初始化
  a = 10;

  return 0;
}
{% endcodeblock %}
#### 函数声明和定义:
在函数声明时，提供一个函数名，而函数的实际定义则可以在任何地方进行。
{% codeblock %}
// 函数声明
int func();
int main ()
{
  // 函数调用
  int i = func();

  return 0;
}
// 函数定义
int func()
{
   return 666;
}
{% endcodeblock %}
#### 变量作用域:
1. 在函数或一个代码块内部声明的变量，称为**局部变量**。
2. 在函数参数的定义中声明的变量，称为**形式参数**。
3. 在所有函数外部声明的变量，称为**全局变量**。

初始化:定义全局变量时，系统会**根据数据类型自动初始化**；定义局部变量时，系统不会自动初始化。
#### C++常量:
定义后不能进行修改的固定值。
**整数常量:** 
* 前缀指定基数：0x 或 0X 表示十六进制，0 表示八进制，不带前缀则默认表示十进制
* 后缀指定修饰符:是 U 和 L 的组合，U 表示无符号整数（unsigned），L 表示长整数（long）。后缀可以是大写，也可以是小写，U 和 L 的顺序任意。

**浮点常量：**由整数部分、小数点、小数部分和指数部分组成。
* 小数形式：必须包含整数部分、小数部分，或同时包含两者。
* 指数形式：必须包含小数点、指数，或同时包含两者。带符号的指数是用 e 或 E 引入的。

**布尔常量：**true/false
**字符常量：**括在单引号中的可以是一个普通的字符（例如 'x'）、一个转义序列（例如 '\t'），或一个通用的字符（例如 '\u02C0'）。
* 宽字符常量：以 L（仅当大写时）开头存储在 **wchar_t **类型的变量中（例如 L'x'）。
* 窄字符常量：存储在 char 类型的简单变量中（例如 'x'）。

**字符串常量：**括在双引号 "" 中的。一个字符串包含类似于字符常量的字符：普通的字符、转义序列和通用的字符。
C++有两种简单定义常量方式:
1. 使用 **#define 预处理器**:#define identifier value
2. 使用 **const 关键字**:const type variable = value;

Tips:常量一般被定义成**大写字母形式**
#### C++ 修饰符类型
修饰符 **signed、unsigned、long 、short**可应用于整型，**signed 和 unsigned**可应用于字符型，long 可应用于双精度型。
修饰符 **signed 和 unsigned** 也可以作为 long 或 short 修饰符的**前缀**。例如：unsigned long int。
#### C++ 类型限定符
用于限定类型和类型成员的声明
* const：限定对象在程序执行期间不能被修改改变。
* volatile：限定变量的值可能以程序未明确指定的方式被改变。(多线程使用)

#### C++ 存储类
存储类定义 C++ 程序中变量/函数的范围（可见性）和生命周期，常有**auto、static、extern、mutable、thread_local (C++11)**。
**auto关键字**用于两种情况：
* 声明变量时根据初始化表达式自动推断该变量的类型。
* 声明函数时函数返回值的占位符。
{% codeblock %}
auto f=3.14;
cout<<typeid(f).name()<<endl;         //double
auto z = new auto(9);     
cout<<typeid(z).name()<<endl;         // int*
{% endcodeblock %}
**static关键字**指示编译器在程序的生命周期内保持局部变量的存在，而不需要在每次它进入和离开作用域时进行创建和销毁。
* static修饰**局部变量**：变量在**全局数据区分配内存**，程序执行第一次时**首次初始化**，以后函数调用不再进行初始化，没有显式初始化则会被**自动初始化为0**。
* static修饰**全局变量**：使变量的作用域**限制在声明它的文件**内。
* static修饰**类的成员变量**：类外初始化，先于类存在，类所有对象共同一个静态成员变量，可通过类名直接调用**公共(public)**静态成员变量。
{% codeblock %}
class A
{
public:
	A(){ count++; }              //先于类存在，在类的构造方法中已经可以调用
	static int count;
};
int A::count = 0;                //类外初始化
int main()
{
	A c;
	A d;
	cout << A::count << endl;    //共用性和直接类名调用
	system("pause");
	return 0;
}
{% endcodeblock %}
* static修饰**类的成员方法**:先于类存在，方法中**不能使用非静态成员(无this指针)**,可通过类名直接调用**公共(public)**静态成员方法。
{% codeblock %}
class A
{
public:
	A(){ count++; }
	static int count;
	static void showCount() 
	{
		cout << "count is " << count << endl;  //不用this指针织带变量
	}
};
int A::count = 0;        
int main()
{
	A c;
	A d;
	A::showCount();                     //类名直接调用
	system("pause");
	return 0;
}
{% endcodeblock %}

#### C++ 运算符
运算符是一种告诉编译器执行特定的数学或逻辑操作的符号,有**算术运算符、关系运算符、逻辑运算符、位运算符、赋值运算符和其他运算符**。
**算数运算符**:+、-、*、/、%(取模,整除后的余数)、++(自增)、--(自减).
**关系运算符**:==、!=、>、<、>=、<=.
**逻辑运算符**:&&(逻辑与，均非零为真)、||(逻辑或，存在非零则为真)、!(逻辑非，逆转逻辑状态).
Tips:&&(||)具有**短路**功能，即若第一个条件为false(true)，则不再计算第二个表达式；&(|)则无论如何**均会计算两个表达式**.
利用这点可将A&&B中的A作为一个条件语句(if)不满足则不执行B，如下
{% codeblock %}
/*不用任何循环语句，不用if，来实现1+2+3+...+10的值*/
#include <iostream>
using namespace std;

int add(int c)
{
    int a=0;
    c&&(a=add(c-1));//递归循环，直到传入c的值为0则结束循环不执行右式
		//(用&则会执行造成溢出错误)
    cout<<c+a<<endl;
    return c+a;
}
int main()
{ 
    add(10);
    return 0;
}
{% endcodeblock %}
**位运算**:
若A = 60，且 B = 13，以二进制表示如下
A = 0011 1100
B = 0000 1101
![](/images/weiyunsuan.png)
**赋值运算**:=、+=、-=、*=、/=、%=、<<=、>>=、&=(按位与或且赋值)、^=(按位异或且赋值)、|=(按位或且赋值)
**杂项运算**:
![](/images/zaxiang.png)
#### C++ 循环
* **while**:
{% codeblock %}
while(condition)
{
   statement(s);
}
{% endcodeblock %}
* **for**:
{% codeblock %}
for ( init; condition; increment )
{
   statement(s);
}
{% endcodeblock %}
* **do...while**:
{% codeblock %}
do
{
   statement(s);

}while( condition );
{% endcodeblock %}
* **循环控制语句**:
1. **break**：跳出循环。
2. **continue**:跳过循环主体的剩余部分，立即重新开始测试条件。

#### C++ 判断
* **if**:
{% codeblock %}
if(boolean_expression)
{
   statement(true);          // 如果布尔表达式为真将执行的语句
}
{% endcodeblock %}
* **if...else**:
{% codeblock %}
if(boolean_expression)
{
   statement(true);          // 如果布尔表达式为真将执行的语句
}
else
{
   statement(false);          // 如果布尔表达式为假将执行的语句
}
{% endcodeblock %}
* **switch**:
{% codeblock %}
switch(expression){
    case constant-expression  :
       statement(s);
       break; // 可选的
    case constant-expression  :
       statement(s);
       break; // 可选的
  
    // 您可以有任意数量的 case 语句
    default : // 可选的 
       statement(s);
}
{% endcodeblock %}
* **[ ? : ]运算符**:
{% codeblock %}
Exp1 ? Exp2 : Exp3;
{% endcodeblock %}

#### C++ 函数
**函数声明**包含函数的名称、返回类型和参数
C++中的函数声明形式：
{% codeblock %}
return_type function_name( parameter list );
{% endcodeblock %}
**函数定义**提供了函数的实际主体。
C++中的函数定义形式：
{% codeblock %}
return_type function_name( parameter list )
{
   body of the function
}
{% endcodeblock %}
**函数参数**:函数内使用的局部变量称作形式参数，有三种调用方式
* 传值调用(默认)：把参数的**实际值**复制给形式参数。在这种情况下，修改函数内的形式参数不会影响实际参数。
* 指针调用：把参数的**地址**复制给形式参数。在函数内，该地址用于访问调用中要用到的实际参数。修改形式参数会影响实际参数。如
函数声明:void swap(<font color=red>int \*x, int \*y</font>)
函数调用:swap(<font color=red>&a,&b</font>)
* 引用调用：把参数的**引用**赋值给形式参数。在函数内，该引用用于访问调用中要用到的实际参数。这意味着，修改形式参数会影响实际参数。如
函数声明:void swap(<font color=red>int &x, int &y</font>)
函数调用:swap(<font color=red>a,b</font>)

**参数默认值**:定义函数时可以为参数列表中每一个参数指定默认值,如
int sum(int a<font color=red>=10</font>,int b<font color=red>=20</font>)
**Lambda 函数与表达式**:Lambda 表达式把函数看作对象。Lambda 表达式可以像对象一样使用，比如可以将它们赋给变量和作为参数传递，还可以像函数一样对其求值。

#### C++ 数字
**数学运算**:引用数学头文件 <cmath>内有许多内置数学函数
![](/images/shuxue.png)
**随机数**:标准库<cstdlib>（被包含于<iostream>中）提供两个帮助生成伪随机数的函数
* void srand(unsigned seed)：设置随机数种子，通常用**time()**初始化(使用time需要引入<ctime>头文件)，如<font color=red>srand( (unsigned)time( NULL ) );</font>
* int rand(void)：从srand (seed)中指定的seed开始，返回一个[seed, RAND_MAX（0x7fff）)间的随机整数。
* 指定生成范围的随机数
{% codeblock %}
(low,up) (rand()%(up-low+1)) + low - 1
[low,up) (rand()%(up-low)) + low
(low,up] (rand()%(up-low))+ low + 1
[low,up] (rand()%(up-low+1)) + low
{% endcodeblock %}

#### C++ 数组
**声明数组**:type arrayName [ arraySize ];
**初始化数组**:type arrayName [ arraySize ] =<font color=red> { </font>data<font color=red> } </font>;
**访问数组元素**:数组名称+索引，arrayName[index];
**数组详解**:
* 多维数组:type arrayName[size1][size2]...[sizeN];
* 数组指针:声明的数组名其实是指向数组第一个元素的指针，即**\*arrayName == &arrayName[0]**
所以也可以使用指针形式访问数组**\*(arrayName + i ) == arrayName[i]**
* 传递数组给函数:是以<font color=red>指针</font>的形式传递的，因此有三种传递方式：**(type *param)(type param[size])(type param[])**
* 函数返回数组:数组以<font color=red>指针</font>的形式返回的，另外C++不支持在函数外返回局部变量的地址，因此返回的数组需要均是**static变量**


#### C++ 字符串
1. **C风格字符串**:实质是一个<font color=red>以'\0'(null)结尾的char数组</font>，例如
{% codeblock %}
//以下msg1和msg2初始化规则相同
char msg1[6] = {'H', 'e', 'l', 'l', 'o', '\0'};
char msg2[] = "Hello";
{% endcodeblock %}
C++有大量函数操作以null结尾的字符串：
![](/images/char.png)
2. **C++引入的 string 类类型**:C++标准库提供了string类型<font color=red>(需引入#include < string \>库)</font>且支持上述函数操作
{% codeblock %}
string str1 = "Hello";
string str2 = str1;              //复制
string str3 = str1 + str2;       //连接
int length = str3.size();        //长度
{% endcodeblock %}

#### C++ 指针
* 内存地址:每个C++变量都有一个内存地址，可用**寻址（&）运算符**访问变量的内存地址
* 指针:是一个特殊的变量，其值为**另一个变量的内存地址**，声明形式为:type *var-name;
* 使用指针:过程一般为声明、存储、访问（对指针使用**间址(*)运算符**可访问指针存储地址的变量值）
{% codeblock %}
string str = "Hello";
string *ip;
ip = &str;
cout << "Address of str : " << &str << endl;
cout << "Value of str : " << str << endl;
cout << "Stored Address of ip : " << ip << endl;     //指针存储的变量的地址
cout << "Address of ip : " << &ip << endl;           //指针本身的地址
cout << "Value of ip : " << *ip << endl;
{% endcodeblock %}
执行结果如下:
![](/images/ip.png)
* Null指针:指针初始化时指定NULL值被称作**空指针**可以避免越界使用指针
{% codeblock %}
type *ptr = NULL;
{% endcodeblock %}
* 指针的算数运算:指针是一个用数值表示的地址，可以执行四种算数运算（++，--，+，-）具体运算数值与指针的类型有关(如整形指针一次递增4个字节，字符指针一次递增1个字节等)
* 指针与数组的异同:
（同）均可通过**指针的算数运算和数组索引**来访问数组
（异）声明数组的数组名的指针是一个指向数组第一个元素地址的**常量指针**，不可改变指向的地址
* 指针数组:可声明一个数组来存放指针，其中每个数组元素都是一个指针，如**type *ptr[size];**
* 多级指针:一个指针指向另一个指针的地址
{% codeblock %}
type var;
type *ptr1 = &var;
type *ptr2 = &ptr1;     //ptr2就是一个多级指针指向一级指针ptr1
{% endcodeblock %}
* 指针参数:声明函数参数为指针类型即可，这种属于**指针调用**函数参数，将参数地址复制给函数的形式参数，在函数内修改形式参数会影响实际参数。
* 函数返回指针类型:声明函数返回类型为指针类型即可，但由于C++**不支持在函数外返回局部变量的地址**，因此可返回指针类型的变量需要声明为**static变量**。

#### C++ 引用
引用变量是一个**实际变量的别名**。一旦把引用初始化为某个变量，就可以使用该引用名称或变量名称来指向变量。
* 引用于指针的区别:
1.引用必须在**创建时初始化**，指针可以在**任何时间初始化**。
2.引出初始化为一个对象后**不能更改**，指针可以在任何时候指向另一个对象。
3.不存在空引用，引用必须连接到一个合法内存。
* 引用创建初始化使用:type<font color=red>&</font> name = variable;
* 引用参数:声明函数参数为引用类型即可，属于**引用调用**的函数参数。这比传一般参数<font color=red>安全</font>。
* 函数返回引用类型:声明函数返回类型为引用类型，但是实际上函数返回一个引用时是返回一个**指向返回值的隐式指针**，这样函数可以放在赋值语句的左边。返回引用最大的好处是**在内存中不产生被返回值的副本，节省内存**，但是**不能返回局部变量的引用**。

#### C++ 日期和时间
C++继承了C语言用于日期和时间操作的结构和函数,需要<font color=red>引入 < ctime \> 头文件</font>
* 有四个与时间有关的类型，其中**clock_t、time_t、size_t **将系统时间和日期表示为某种**整数**，**tm**结构类型将日期和时间以C结构形式保存。
* C/C++中关于日期和时间的重要函数:
1.**time()**: 返回从 1970-01-01 到 当前的日历时间的**秒数**
{% codeblock %}
//函数原型:time_t time(time_t *t)
time_t nowSeconds =  time(NULL);
cout << nowSeconds << endl;
{% endcodeblock %}
![](/images/time.png)
2.**ctime()**:返回一个格式为[Www Mmm dd hh:mm:ss yyyy]基于参数timer的时间的**字符串**
{% codeblock %}
//函数原型:char *ctime(const time_t *timer)
#pragma warning(disable:4996)    //接触安全警报限制
time_t curtime = time(NULL);
cout << ctime(&curtime) << endl;
{% endcodeblock %}
![](/images/ctime.png)
3.**localtime()**: struct tm \*localtime(const time_t \*timer)，将参数timer的值分解为tm结构，并用**本地时区**表示
{% codeblock %}
//函数原型:struct tm *localtime(const time_t *timer)
#pragma warning(disable:4996)    //接触安全警报限制
time_t curtime = time(NULL);
struct tm *info = localtime(&curtime);
cout << "tm_sec:" << info->tm_sec << endl;
cout << "tm_min:" << info->tm_min << endl;
cout << "tm_hour:" << info->tm_hour << endl;
cout << "tm_mday:" << info->tm_mday << endl;
cout << "tm_mon:" << info->tm_mon << endl;
cout << "tm_year:" << info->tm_year << endl;
{% endcodeblock %}
![](/images/localtime.png)
4.**clock()**: 返回程序执行起**处理器时钟所使用的时间**，为了获取CPU所使用秒数需要除以CLOCKS_PER_SEC常量
{% codeblock %}
//函数原型:clock_t clock(void)
#pragma warning(disable:4996)    //接触安全警报限制
clock_t start_t, end_t;
start_t = clock();
cout << "程序启动：" << start_t << endl;
for(int i=0; i< 10000000; i++)
{
}
end_t = clock();
cout << "程序结束：" << end_t << endl;
double total_t;
total_t = (double)(end_t - start_t) / CLOCKS_PER_SEC;
cout << "CPU占用时间:" << total_t << endl;
{% endcodeblock %}
![](/images/clock.png)
5.**asctime()**：返回一个基于timeptr的时期和时间的**字符串**的指针
{% codeblock %}
//函数原型:char *asctime(const struct tm *timeptr)
#pragma warning(disable:4996)    //接触安全警报限制
time_t curtime = time(NULL);
struct tm *info = localtime(&curtime);
cout << asctime(info) << endl;
{% endcodeblock %}
![](/images/asctime.png)
6.**gmtime()**:使用timer来填充tm的结构，并协调**世界时**(UTC)也被称作格林尼治标准时间(GMT)表示
{% codeblock %}
//函数原型:struct tm *gmtime(const time_t *timer)
#pragma warning(disable:4996)    //接触安全警报限制
time_t curtime = time(NULL);
struct tm *info = gmtime(&curtime);
cout << asctime(info) << endl;
{% endcodeblock %}
![](/images/gmtime.png)
7.**mktime()**:将tm结构的timer转换成一个依据**本地时区**的time_t值
{% codeblock %}
//函数原型:time_t mktime(struct tm *timeptr)
#pragma warning(disable:4996)    //接触安全警报限制
time_t curtime = time(NULL);
struct tm *info = localtime(&curtime);
time_t localtime = mktime(info);  
cout << ctime(&localtime) << endl;
{% endcodeblock %}
![](/images/mktime.png)
8.**difftime()**: 返回time1和time2之间的**相差秒数**
{% codeblock %}
// double difftime(time_t time1, time_t time2)
#pragma warning(disable:4996)    //接触安全警报限制
#include <windows.h>             //sleep函数
time_t start_t, end_t;
time(&start_t);
cout << "程序启动：" << start_t << endl;
Sleep(5632);
time(&end_t);
cout << "程序结束：" << end_t << endl;
double diff_t = difftime(end_t, start_t); //以秒为单位精度是double我也不太清楚为什么....
cout << "程序占用时间:" << diff_t << endl;
{% endcodeblock %}
![](/images/difftime.png)
9.**strftime()**: size_t strftime(char \*str, size_t maxsize, const char \*format, const struct tm \*timeptr)，根据format中定义的格式化规则，格式化数据结构timeptr表示的时间，并把它存储在str中
{% codeblock %}
time_t now = time(NULL);
struct tm *info = localtime(&now);
char buffer[80];
strftime(buffer, 80, "%x - %I:%M%p", info);
cout << "格式化时间：" << buffer << endl;
{% endcodeblock %}
![](/images/strftime.png)

#### C++ 基本输入输出
* C++的I/O 发生在**流**中，流是**字节序列**。
输入操作：字节流是从设备（如键盘、磁盘驱动器、网络连接等）流向内存。
输出操作：字节流是从内存流向设备（如显示屏、打印机、磁盘驱动器、网络连接等）。
* I/O 库头文件：
![](/images/io.png)
1.**标准输出流(cout)**:是ostream类的实例，与运算符<<将对象连接到标准输出设备
2.**标准输入流(cin)**:是istream类的实例，与运算符>>将对象连接到标准输入设备
3.**标准错误流(cerr)**:是ostream类的实例，是**非缓冲的**，每个流插入cerr会立即输出
4.**标准日志流(clog)**:是ostream类的实例，是**缓冲的**，每个流插入clog会先存储到缓冲区直到其填满或者刷新时才会输出

#### C++ 结构
用户自定义的数据类型，允许存储不同类型的数据项，格式如下
{% codeblock %}
struct type_name {
member_type1 member_name1;
member_type2 member_name2;
member_type3 member_name3;
.
.
} object_names;
{% endcodeblock %}
* 访问结构成员:使用**成员访问运算符(.)**，如
{% codeblock %}
object_names.member_name
{% endcodeblock %}
* 访问指向结构的指针:可定以指向结构的指针，则使用**指针访问运算符(->)**，如
{% codeblock %}
struct type_name *struct_ptr = &object_names;
struct_pointer->title
{% endcodeblock %}