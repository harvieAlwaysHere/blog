---
title: 剑指Offer算法记录(一)数据结构
tags:
  - 数据结构
date: 2018-02-07 16:20:05
categories: ItEassy
---
![](/images/offer.png)
### 本文主要记录阅读剑指Offer这本书所遇到的算法解释并作一些个人的拓展
---
### 第二章
#### 2.2.1 关于sizeof的小知识
* sizeof 对于**空类型**(无成员变量和成员函数)的实例 = **1字节**：由于声明空类型的实例必须在内存中占用一定空间否则无法使用，占用多少内存由**编译器决定**，在VS中每个空类型实例占用1字节。
* sizeof 对于**空类型+构造和析构函数**的实例 = **1字节** ：调用函数只需知道函数地址即可，函数地址只与类型有关而与类型的实例无关，因此sizeof实例还是1字节。
* sizeof 对于**空类型+构造函数+虚析构函数(虚函数)** = **1个指针大小**：一个类型中有虚拟函数就会为该类型生成**虚函数表**，并为这个类型的每个实例中添加一个**指向虚函数表的指针**，指针大小与机器有关，32为指针为4字节，64位指针为8字节。
{% codeblock %}
class B             //空类型
{
};
class C            //空类型+构造和析构函数
{
public:
	C(){};
	~C(){};
};
class D           //空类型+构造函数+虚析构函数(虚函数)
{
public:
	D(){};
	virtual  ~D(){};
};
int main()
{
	B b;
	C c;
	D d;
	cout << "空类型大小:" << sizeof(b)<<endl;
	cout << "空类型+构造和析构函数大小:" << sizeof(c) << endl;
	cout << "空类型+构造函数+虚析构函数(虚函数)大小:" << sizeof(d) << endl;
	system("pause");
	return 0;
}
{% endcodeblock %}
![](/images/daxiao.png)

总结：
空的类是会占用内存空间的，而且大小是1，原因是C++要求每个实例在内存中都有独一无二的地址。
1. 类内部的成员变量：
\*普通的变量：是要占用内存的，但是要注意对齐原则（这点和struct类型很相似）。
\*static修饰的静态变量：不占用内容，原因是编译器将其放在全局变量区。
2. 类内部的成员函数：
\*普通函数：不占用内存。
\*虚函数：要占用4个字节，用来指定虚函数的虚拟函数表的入口地址。所以一个类的虚函数所占用的地址是不变的，和虚函数的个数是没有关系的
---
#### 2.2.1 关于复制函数的小知识
C++标准不允许复制构造函数传值参数，否则会造成递归重复调用，编译错误。
{% codeblock %}
class E
{
private:
  int num;
public:
  //构造函数
  E(int x) :num(x)
  {
  	  cout << "constructor call" << endl;
  }
  //复制构造函数 传引用调用
  E(E& x)
  {
 	 num = x.num;
  	 cout << "copy constructor call" << endl;
  }
  //赋值运算符重载
  E& operator = (E& x)
  {
 	 num = x.num;
  	 cout << "assignment operator call" << endl;
  	 return *this;
  }
  //函数调用
  void showNum(E x)
  {
	
  } 

};
int main()
{
 	E a(1);       //1式
 	E b(2);       //2式
 	b = a;        //3式
  	E c = a;      //4式
 	c.showNum(a); //5式
 	system("pause");
 	return 0;
}
{% endcodeblock %}
运行结果如图：
![](/images/fuzhi.png)
* 1/2式 => 若是实例**创建并初始化**时调用相应参数的**构造函数**     
* 3式   => 若是实例已经创建初始化后再用=赋值，则调用**=重载赋值函数**
* 4式   => 若是实例用另一个**同类实例初始化**，则调用**复制构造函数**
* 5式   => 若是实例使用方法调用另一个实例，则首先调用**复制构造函数**将实参复制给形参后执行相关操作

因此就可以解释为什么C++不允许复制构造函数传值了，若是传值，则调用E c = a或者c.showNum(a)或者的时候，a作为参数传值给c的复制构造函数的参数E x，因为x没有被初始化，所以要调用**x的复制构造函数**将a复制给x，即x.E(a)，然而x的复制构造函数也是传值的，因此又要将a作为参数传值给c的复制构造函数的参数x的复制构造函数的参数E x，又因为这个x也没有被初始化，又要调用这个x的复制构造函数，造成了无限的递归。
![](/images/gouzao.jpg)
因此复制构造函数的参数使用**引用调用**不是为了减少一次内存的复制，而是为了避免复制构造函数无限递归调用的情况出现。
下面这几种情况下会**调用复制构造函数**:
（1）显式或隐式地用同类型的一个对象来初始化另外一个对象。如上例中的E c=a;
（2）作为实参传递给一个函数。如上例中的c.showNum(a);
（3）在函数体内返回一个对象时，也会调用返回值类型的拷贝构造函数
（4）初始化序列容器中的元素时。比如vector<string> svec(5)，string的缺省构造函数和拷贝构造函数都会被调用。
（5）用列表的方式初始化数组元素时。string a[] = {string(“hello”),string(“world”)};会调用string的拷贝构造函数。

---
#### 面试题 1：赋值运算符函数
![](/images/mianshi1.png)
![](/images/mianshi1+.png)
赋值运算符函数基础4要数:
1.返回值类型声明为该类型的引用并在函数结束前返回实例自身引用：只有这样才能实现连续赋值运算
2.参数类型声明为常量引用:常量是因为在函数体内不改变传入实参状态，引用是可以减少一次从形参到实参的复制构造函数的调用，节省空间开销提高代码效率
3.释放实例自身已有内存:在分配新内存之前释放已有空间，避免内存泄露
4.需要判断传入参数和当前实例(*this)是不是同一个实例:若是同一个则不进行赋值直接返回，若没有判断则在释放实例自身内存时传入的参数的内存也被释放了，就再也找不到需要赋值的内容了
结合以上4点的解法
{% codeblock %}
CMyString& CMyString::operator =(const CMyString &str)  //返回值+参数(1.2.)
{
 	if(this == $str) return *this;		//判断是否是自身赋值自身(4.)
	
 	delete []m_pData;		//被赋值的实例的成员变量delete释放内存(3.)
 	m_pData = NULL;			//初始化变量
 	m_pData = new char[strlen(str.m_pData)+1];	//new分配内存
 	strcpy(m_pData,str.m_pData);		//赋值
	
 	return *this;			//返回自身实例用于连续赋值运算(1.)
}
{% endcodeblock %}

考虑**异常安全性**:若new char时内存不足导致抛出异常，m_pData将是空指针，程序十分容易崩溃，此时由于被赋值的实例的成员变量已经被delete了，实例改变了自身的状态，违背了异常安全原则。
解决方案:
1.先new分配内存再delete释放内存，当new失败时我们能确保实例不会被修改原先状态
2.先创建一个临时实例，再交换临时实例和原来的实例，如下
{% codeblock %}
CMyString& CMyString::operator =(const CMyString &str)  
{
 	if(this != $str)
 	{
 		CMyString strTemp(str);        //调用复制构造函数新创建一个临时实例
 	  	char* pTemp = strTemp.m_pData; //利用一个中间变量pTemp将变量值交换
 		strTemp.m_pData = m_pData;
 		m_pData = pTemp;
 	}								
//在if作用域外，由于strTemp是局部变量会自动调用析构函数释放内存，
//其中strTemp中的成员变量m_pData所指向的内存就是原来被赋值实例成员函数的内存，刚好一并释放

 	return *this;
}
{% endcodeblock %}

---

#### 面试题 2：实现Singleton（单例）模式
首先单例模式最基本的有3点：
1.构造函数私有:不能被外界随意调用
2.单例对象实例:此类型只能生成一个实例
3.单例获取函数:只能获取到同一个实例
基于这三点我们可以写出**第一版单例模式**
{% codeblock %}
public class Singleton1
{
 	private Singleton1(){}   //私有构造函数
 	private static Singleton1 instance = NULL; //单例对象实例
 	public static Singleton1 getInstance()     //单例获取函数
 	{
 		if(instance == NULL) instance = new Singleton1();
 		return instance;
 	}
}
//其中第四行我们也可以写成private static Singleton1 instance = new Singleton1();
//这样在getInstance()中就不需要判空操作，这属于饿汉模式(主动创造)，一开始初始值是NULL属于懒汉模式(等人调用才创造)
{% endcodeblock %}
**第二版单例模式**:第一版的单例模式是**非线程安全**的，当最初情况两个线程同时判断instance是否为空时(针对懒汉模式)，则两个线程会创建两个实例不满足单例模式要求，因此在多线程下我们要加上一个**同步锁**
{% codeblock %}

public class Singleton2
{
 	private Singleton2(){}   
 	private static Singleton1 instance = NULL; 
 	
 	private static object syncObj = new object();  //C#同步锁	

 	public static Singleton2 getInstance()     
 	{
 		if(instance == NULL) //一次检测:只有instance为NULL时需要加锁操作，节省开销
 		{
 			lock(syncObj)		
 			{
 				//二次检测:当两个进程同时访问，线程A创建完对象开锁后
 				//线程B还是会进入临界区创建对象，因此需要第二次检测
 				if(instance == NULL)   
 				instance = new Singleton1();
 			}
 		}

 		return instance;
 	}
}
//其中第十二行只是C#同步锁的操作，JAVA中可使用synchronized(Singleton2.class){}操作
{% endcodeblock %}
**第三版单例模式**:<font color="red">C#</font>中有一个函数确保只调用一次，就是**静态构造函数**(饿汉模式)
{% codeblock %}
public class Singleton3
{
 	private Singleton3(){}   
 	private static Singleton3 instance = new Singleton3(); //由于C#在调用静态构造函数时初始化静态变量，.NET运行时能够确保只调用一次静态构造函数
 	public static Singleton3 getInstance()    
 	{
 		return instance;
 	}
}
{% endcodeblock %}
**第四版单例模式**:**按需创造/懒加载**
<font color="red">JAVA</font>中可以使用**静态内部类**
1.因为从外部无法访问静态内部类，只有调用Singleton.getInstance方法才能得到单例对象INSTANCE
2.INSTANCE对象初始化并不是在类Singleton被加载时，而是在调用getInstance方法时加载静态内部类LazyHolder时才被初始化，这种方式是利用**classloader的加载机制**实现懒加载（需要时才创建），从而保证偶见单例的线程安全
{% codeblock %}
public class Singleton4
{
 	private Singleton4(){}   
 	
 	private static class LazyHolder  //静态内部类
 	{
 		private static final Singleton INSTANCE = new Singleton4();
 	}
 
 	public static Singleton4 getInstance()    
 	{
 		return LazyHolder.INSTANCE;
 	}
}
{% endcodeblock %}
<font color="red">C++</font>中可以使用**嵌套类型**
{% codeblock %}
public class Singleton4
{
 	private Singleton4(){}   
 	
 	class Nested  //嵌套类型
 	{
 		static Newted(){}   //嵌套类型的构造函数
 		internal static readonly Singleton4 instance = new Singleton4();
 	}
 
 	public static Singleton4 getInstance()    
 	{
 		return Nested.instance;
 	}
}
{% endcodeblock %}
**第五版单例模式**:无论怎么样设计单例模式，均可以通过**反射**打破单例
反射打破单例过程：
{% codeblock %}
//第一步：获得单例类的构造器
Constructor con = SingletonEnum.class.getDeclaredConstructor();
//第二步：把构造器设置为可访问
con.setAccessible(true);
//第三步：使用newInstance构造不同的对象
SingletonEnum singleton1 = (SingletonEnum)con.newInstance();
SingletonEnum singleton2 = (SingletonEnum)con.newInstance();
//验证是否是同一对象 => false
System.out.println(singleton1.equals(singleton2));
{% endcodeblock %}
针对防止反射的方式构建对象，在<font color="red">JAVA</font>中可以使用**枚举**的方式构建类，因为**JAM会住址反射获取枚举类的私有构造方法**，这还可以**实现线程安全性**，但是唯一的缺点就是并非使用懒加载，单例对象是在枚举类被加载的时候进行初始化的。
{% codeblock %}
//枚举实现单例模式
public enum SingletonEnum{
 	INSTANCE;
}
{% endcodeblock %}
#### 总结：
<font color="red">单例模式升级步骤</font>——**基本模式**(1.)+**线程安全**(2.)+**懒加载(提升使用效率)**(3.4.)+**防止反射构建**(5.)
---

#### 2.3 数组和指针区别
![](/images/shuzuzhizhen.png)
输出 "20,4,4" 
20：data1是一个数组，包含五个整数，每个整数占4字节，一共20字节
4：data2是一个指针指向data1数组的第一个数字，指针大小为4字节
4：在C/C++中数组作为参数传递时自动退化为同类型的指针，因此为4字节

---


#### 面试题3：二维数组中的查找
![](/images/mianshi3.png)
一般的思路是从左上角开始做比较，但是减小排除的范围无规律，若是从**右上角**或者**左下角**做比较，则可以以行或者列减小排查范围，而二维数组以**连续内存存储**的特性让我们可以通过下标访问数组以矩阵形式的空间位置。
{% codeblock %}
bool Find(int* matrix, int rows, int columns, int number)
{
  bool found = false;
 	
  if(matrix != NULL && rows >0 && columns >0) //矩阵有效性检测（若输入空指针）
  {
 	int row = 0;
 	int column = column -1;
 	while(row < rows && column >=0) //循环停止条件:最后一行或者第一列
 	{
 	  if(matrix[row * columns + column] == number) //第row行第column列，相当于[row][column]
 	  {
 		  found = true;
 		  break;
 	  }
 	  else if(matrix[row * columns + column] >number)
 	 		  column--;
 	  else row++;
 	}
  }

  return found;
}
{% endcodeblock %}

---

#### 2.3.2字符串小知识
C/C++中每个字符串以'/0'作为结尾，这样可以方便地找到字符串的结尾，但有额外字符开销，易越界。
C/C++将常量字符串放到一个单独的内存区域节省内存，当几个指针赋值给相同的常量字符串时，他们实际上会指向相同的内存地址，但是用常量内存初始化数组却会创建新空间。

---
#### 面试题4：替换空格
![](/images/mianshi4.png)
网络编程中，URL参数中特殊字符串(空格、#等)需要替换成服务器端可以识别的字符，转换的规则是**在'%'后面加上字符ASCII码的两位十六进制表示**，如空格的ASCII码为32，十六进制为0x20，因此空格被替换成"%20"。
解题思路:题目理解有两种，第一种是创建新的字符串并在新的字符串上做替换，则我们可以**分配足够多的内存**，第二种是在原字符串上做替换，则我们需要保证输入字符串后有**足够多的空余内存**并且**不能覆盖原字符串的内存**。
**解法一**:字符串从前往后遍历找到一个空格则空格后的字符集体向后移动两个字节大小。时间复杂度为**O(n^2)**，**多次移动次数**，不是好的解决方案。
**解法二**:先遍历一次字符串，统计出字符串中空格总数由此计算出**替换后字符串的长度**，之后**从字符串末尾开始复制和替换**，这样所有字符只移动一次，算法的时间效率是**O(n)**。
具体的复制和替换过程为使用**两个指针**
{% codeblock %}
void ReplaceBlank(char string[], int maxLength)
{
 	if(string == NULL && length <= 0)   //输入参数有效性检测性
 		return;

 	//originalLength为字符串原先长度
 	int originalLength = 0;
 	int numberOfBlank = 0;
 	int i = 0;
 	while(string[i] != '\0')
 	{
 		originalLength++;
 		if(string[i] == ' ') numberOfBlank++;
 		i++;
 	}

 	//newLength为替换后的字符串长度
 	int newLength = originalLength + numberOfBlank * 2;
 	if(newLength > maxLength)     //如果替换后字符串长度大于字符串总容量
 		return;
 	
 	int indexOfOriginal = originalLength;
 	int indexOfNew = newLength;
 	while(indexOfOriginal >= 0 && indexOfNew > indexOfOriginal)//替换完成条件
 	{
 		//若是空格则后面的标记添加并前移三个字节
 		if(string[indexOfOriginal] == ' ')  
 		{
 			string[indexOfNew --] = '0';
 			string[indexOfNew --] = '2';
 			string[indexOfNew --] = '%';
 		}
 		else
 		{	//若不空格则复制前面指针的字符到后面指针的位置，并前移指针
 			string[indexOfNew --] = string[indexOfOriginal]; 
 		}
 		
 		-- indexOfOriginal;
 	}

 	
}
{% endcodeblock %}
---

#### 2.3.3 链表小知识
链表是由指针把若干个节点连接成链状结构，链表的创建、插入节点、删除节点等操作只需要20行代码就可以实现，比较适合面试。
链表是一种动态结构，创建时无需知道链表的长度，每添加一次节点再分配新内存，然后调整指针的指向。
单向链表的节点定义如下
{% codeblock %}
struct ListNode
{
 	int Value;
 	ListNode* Next;
}
{% endcodeblock %}
往链表末尾添加一个节点的函数如下
要点：1.头指针的传参 2.插入的节点是第一个节点的情况(空链表/头指针为空)
{% codeblock %}
void addToTail(ListNode** pHead, int value)  //由于头指针可能改变，因此要以传指针的形式传参，否则出了函数头指针依然是空指针
{
 	ListNode* pNew = new ListNode();
 	pNew->Value = value;
 	pNew->Next = NULL;	//注意新节点的Next初始化为NULL

 	if(*pHead == NULL)  //若是往一个空链表插入节点，则头指针指向新节点
 	{
 		*pHead = pNew;
 	}
 	else  	//否则找到头指针指向第一个节点，开始遍历找到最后一个节点，将节点的Next指向新节点
 	{
 		ListNode* pNode = *pHead;

 		while(pNode->Next != NULL)
 			pNode = pNode->Next;

 		pNode->Next = pNew;
 	}
}
{% endcodeblock %}
在链表中找到第一个含有某值的节点并删除该节点的函数如下
要点：
{% codeblock %}
void removeNode(ListNode** pHead, int value)
{
 	if(pHead == NULL || *pHead == NULL)  //空链表直接返回
 		return;

 	ListNode* pToBeDelete = NULL;  //找到需要删除的节点
 	if((*pHead)->Value == value)  //当第一个节点是需要删除的节点，则需要改变头指针的指向
 	{
 		pToBeDelete = *pHead;
 		*pHead = (*pHead)->Next;
 	}
 	else 						//否则开始遍历节点
 	{
 		ListNode* pNode = *pHead;
 		while(pNode->Next != NULL && pNode->Next->Value != value)  //遍历到最后一个节点或者找到需要删除的节点的前一个节点则停止遍历
 		  pNode = pNode->Next;
 		
 		if(pNode->Next != NULL && pNode->Next->Value ==value)  //找到需要删除的节点的前一个节点
 		{
 		  pToBeDelete = pNode->Next;
 		  pNode->Next = pNode->Next->Next;  //这里包含了当需要删除的节点是最后一个节点的情况，若是最后一个节点则它的前一个节点会指向NULL
 		}
 	}

 	if(pToBeDelete != NULL)
 	{
 		delete pToBeDelete;
 		pToBeDelete = NULL;
 	}
}
{% endcodeblock %}
---

#### 面试题5：从尾到头打印链表
![](/images/mianshi5.png)
面试中如果我们打算**修改输入数据**，最好先问面试官允不允许。
若允许我们可以将链表中的节点指针**反转改变链表方向**就可以了。
通常打印是一个只读操作，假设面试官要求这个题目不能改变链表结构。
思路：遍历是**从头到尾**，输出却是**从尾到头**，这是典型的<font color='red'>后进先出的栈结构</font>，然后<font color='red'>递归的本质就是栈结构</font>，于是我们可以每访问一个节点先递归输出它后面的节点，再输出该节点自身。
显示用栈调用代码如下
{% codeblock %}
//Reversingly-倒置地
void printListReversingly(ListNode* pHead) //不用改变头指针，可传值
{
  std::stack<ListNode*> nodes;

  ListNode *pNode = pHead;
  while(pNode != NULL)
  {
    node.push(pNode);  //入栈
    pNode = pNode->Next;
  }

  while(!node.empty())
  {
    pNode = nodes.top();  //取栈顶元素
    cout << pNode->Value << endl;  //输出节点值
    nodes.pop();  //弹出栈顶元素
  }
}
{% endcodeblock %}
基于递归的栈调用代码如下
{% codeblock %}
//Recursively-递归地
void printListReversinglyRecursively(ListNode* pHead) //不用改变头指针，可传值
{
  if(pHead != NULL)  //判断是不是空链表
  {
    if(pHead->Next != NULL) printListReversinglyRecursively(pHead->Next);  //判断是不是最后一个结点，若是最后一个结点则递归结束		
    cout << pHead->Value << endl;
  }	
}
Tips:链表长时，导致函数调用层级很深，有可能导致函数调用栈移除，鲁棒性(程序稳定性)不好。
{% endcodeblock %}
---

#### 2.3.4 树小知识
树是一种数据结构:
* 除了根节点外每个节点只有一个父节点，根节点没有父节点
* 除了叶节点外每个节点有一个或多个子节点，叶节点没有子节点
* 父节点与子节点之间用指针连接

二叉树：树的特殊结构，每个节点最多有两个子节点
遍历方式：
* 前序：根-左-右
* 中序：左-根-右
* 后序：左-右-根
* 宽度优先：按照层的顺序从顶到底遍历，同一层的节点按从左到右遍历
* 
二叉搜索树：左节点小于等于根节点，右节点大于等于根节点的二叉树
堆：最大堆中根节点的值最大，最小堆中根节点的值最小
红黑树：把树中的节点定义为红、黑两种颜色，并通过规则确保从根节点到叶节点的最长路径的长度不超过最短路径的两倍，C++中STL中set、multiset、map、multimap等数据结构都是基于红黑树实现的
---

#### 面试题6：重建二叉树
![](/images/mianshi6.png)
思路：前序遍历可以确定**树的根节点**(第一个数字)，中序遍历在知道了根节点的值后可以知道**左子树的结点值**(根节点左边的所有值)和**右子树的结点值**(根节点右边的所有值)，之后用<font color='red'>递归的方式</font>完成剩下子树的建立
{% codeblock %}
//树的主构造函数
BinaryTreeNode* Construct(int* preorder, int* inorder, int length)
{
  if(preorder == NULL || inorder == NULL || length <= 0)  //输入有效性检测
    return NULL;

  return ConstructCore(preorder, preorder+length-1, inorder, inorder+length-1);
}
//树的核心构造函数，通过传入前序遍历的头尾指针、中序遍历的头尾指针递归建树
BinaryTreeNode* ConstructCore(int* startPreorder, int *endPreorder, int* startInorder, int* endInorder)
{
  //前序遍历的第一个数字是根节点的值
  int rootValue = startPreorder[0];
  BinaryTreeNode* root = new BinaryTreeNode();
  root->m_nValue = rootValue;
  root->m_pLeft = root->m_pRight = NULL;
  
  if(startPreorder == endPreorder)
  {
    //若是输入的树前序只有一个值、中序也只有一个值且这两个值是同一个值
    //则输入的这个值就是这棵树的根节点，否则是无效输入
    if(startInorder == endInorder && *startPreorder == *startInorder) return root;
    else throw std::exception("Invalid input");
  }

  //在中序遍历中找到根节点的值
  int* rootInorder = startInorder;
  while(rootInorder <= endInorder && *rootInorder != rootValue) rootInorder++;

  //如果找到的节点在中序的末尾且值并不是根节点的值，返回错误
  if(rootInorder == endInorder && *rootInorder != rootValue)
  throw std::exception("Invalid input");

  //可以通过找到的节点确定左子树长度和左子树范围
  int leftLength = rootInorder - startInorder;
  int* leftPreorderEnd = startPreorder + leftLength;
  if(leftLength >0)
  {
    //构建左子树
    root->m_pLeft = ConstructCore(startPreorder+1,leftPreorderEnd,startInorder,rootInorder-1);
  }
  if(leftLength < endPreorder-startPreorder)
  {
    //构建右子树
    root->m_pRight = ConstructCore(leftPreorderEnd+1,endPreorder,rootInorder+1,endInorder);
  }

  return root;
}
{% endcodeblock %}
让我们来看看ConstructCore做了什么事
![](/images/saomiao1.jpg)
---

#### 2.3.5 栈和队列小知识
栈：先进先出，即最后入栈(push)的元素会第一个被弹出(pop)
队列：先进先出，即第一个进入队列的元素会第一个出来
---

#### 面试题7：用两个栈实现队列
![](/images/mianshi7.png)
思路：插入元素直接插入第一个栈即可，删除元素则若是第二个栈有元素就pop，若是第二个栈没有元素，则将第一个栈的元素全部pop并push进第一个栈，之后按照第二个栈有元素的方式pop
{% codeblock %}
//元素入队
template<typename T> void CQueue<T>::appendTail(const T& element)
{
  stack1.push(element);
}

//元素出队
template<typename T> T CQueue<T>::deleteHead()
{
  if(stack2.size() <= 0)  //stack2空则将stack1全部弹出压如stack2
  {
    while(stack1.size() > 0)
    {
      T& data = stack1.top();
      stack1.pop();
      stack2.push(data);
    }
  }

  //此时若是stack2还是空，则证明队列是空的
  if(stack2.size() = 0) throw new exception("queue is empty");  

  T head = stack2.top();  //否则弹出栈顶元素
  stack2.pop();

  return head;
}
{% endcodeblock %}
两个栈实现一个队列像是一个是**插入栈**一个是**删除栈**，而<font color='red'>两个队列实现一个栈</font>呢？
思路如下
![](/images/saomiao2.jpg)