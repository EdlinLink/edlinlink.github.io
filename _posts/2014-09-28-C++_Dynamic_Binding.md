---
layout: post
title:  "C++ Dynamic Binding"
date:   2014-09-28 12:00:00
tags:	tech
myTag:	c++

---

# C++ Dynamic Binding

---------------------------------------------------------------------------

上星期百度面试的时候，面试官问了一个如下题目:

**有class A，class B，B继承A，A和B各自都有一个call()函数。如果现在一个A指针p指向一个B对象。如果A的call()函数不是虚函数，那么p调用call()调用的是A的call()还是B的call()？如果A的call()函数是虚函数，又怎么样？**

用代码说话就是，以下代码的输出结果是什么:

	#include <iostream>
	using namespace std;

	class A{
	public:
		virtual void call(){
			cout << "A's call" << endl;
		}
	};

	class B:public A{
	public:
		void call(){
			cout << "B's call" << endl;
		}
	};

	int main(){
		A* p = new B();
		p->call();

		return 0;
	}

--------------------------------------------

在思考上面题目的时候，引用开源中国社区一篇文章的内容，我们来理解四个名词：

1. 对象的静态类型: 对象在声明时采用的类型。在编译期时确定。
2. 对象的动态类型: 目前对象的类型。在运行期决定。
3. 静态绑定: 绑定的时对象的静态类型，某特性(比如函数)依赖于对象的静态类型，发生在编译期。
4. 动态绑定: 绑定的是对象的动态类型，某特性(比如函数)依赖于对象的动态类型，发生在运行期。

Talk is easy show the code

	class C{};

	class D : public C{};

	class E : public C{};

	D* pD = new D();		//	pD的静态类型是它声明的类型D*，动态类型也是D*
	E* pE = new E();		//	pE的静态类型是它声明的类型E*，动态类型也是E*
	C* pC = pD();			//	pC的静态类型是它声明的类型C*，动态类型是它指向的对象pD的类型D*
	pC = pE;				//	pC的动态类型是可以更改的，现在它的动态类型是E*

--------------------------------------------

所谓的动态绑定就是程序在运行的时候才知道真正要执行的是什么。在C++中，函数的调用默认是不使用动态绑定的。要触发动态绑定，需要两个条件:

1. 函数指定为虚函数，添加关键字virtual;
2. 通过基类类型的引用或者指针调用.

virtual可以用来定义类函数和应用到虚继承。

友元函数，构造函数，static静态函数 不能使用virtual关键字修饰；普通成员函数和析构函数可以用virtual关键字修饰。


---------------------------------------------

看完上面的文字，我们回过头来看文章一开始的题目。如果没有使用virtual关键字修饰，那么指针将使用静态绑定(即声明是什么类型的就调用什么类型的函数)；如果使用virtual关键字修饰，那么指针将使用动态绑定(即对象的类型是什么就调用什么类型的函数)。

题目中指针p的静态类型是A*，动态类型是B*，class A使用了virtual修饰call()函数，所以指针p使用动态绑定，p调用call()函数时，调用的是B的call()函数，所以得到的结果是输出

	B's call

如果class A没有使用virtual关键字修饰call()函数，那么指针p将使用静态绑定，调用A的call()函数。

---------------------------------------------

还有其他一些小细节需要注意: 如果声明派生类时不写继承方式，其默认的继承方式是private。

	class B : A
	{
		//...
	}
	
	//	默认继承方式是private


## Reference 

1. [深入理解C++的动态绑定和静态绑定. 开源中国社区](http://www.oschina.net/question/54100_20313)
2. [浅析c++中virtual关键字. djh512. CSDN博客](http://blog.csdn.net/djh512/article/details/8973606)
5. [声明派生类时如不写继承方式. 百度知道](http://zhidao.baidu.com/link?url=Eu_f75Wg6E1ZVMFXcQPI5hF3AdiKBoABL1ZdhiKI4CAfBX3BnCKe0kbEvHNP84Y7Avbzai5srJvqVRx-ntqbuK)
