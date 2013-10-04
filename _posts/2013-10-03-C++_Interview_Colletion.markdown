---
layout: post
title:  "C++ Interview Collection 2011"
date:   2013-10-03 00:00:00
myTag:	c++

---

C++ Interview Collection 2011
-----------------------------

### 1. new、delete、malloc、free的关系

new会调用对象的构造函数，delete会调用析构函数，而malloc和free只会分配、释放内存。  

malloc与free是C/C++的标准库函数，new和delete是C++的运算符。它们都可以用于申请动态内存和释放内存。但是对象在创建的同时如果需要自动执行构造函数、在销亡前要自动执行析构函数，由于malloc/free是库函数而不是运算符，不在编译器控制权限之内，不能讲执行构造函数和析构函数的任务强加于malloc/free。因此C++需要一个能够完成动态内存分配的初始化工作的运算符new，以及一个能够完成清理与释放内存工作的运算符delete。

### 2. delete与delete[]的区别

delete只会调用一次析构函数，而delelte[]会调用每一个成员的析构函数。

在More Effective C++中有更为详细的解释:"当delete操作符用于数组时，它为每一个数组元素调用析构函数，然后调用operatordelete来释放内存。"delete与new配套，delete[]与new[]配套。

对于内建简单数据类型(int, float...)，delete和delete[]功能是相同的。对于自定义的复杂数据类型，delete和delete[]不能互用。简单来说delete[]删除一个数组，delete删除一个指针。内部数据类型因为没有析构函数，所以问题不大。如果你在用delete时没用括号，delete会认为指向的是单个对象，否则，它会认为指向的是一个数组。

### 7. 多态，虚函数，纯虚函数

多态：同一操作作用与不同的对象，可以有不同的解释，产生不同的结果。在运行中可以通过指向基类的指针，来调用实现派生类中的方法。简单来说就是允许子类类型的指针赋值给父类类型的指针。多态性在C++中是通过虚函数实现的。

虚函数：在基类中冠以关键字virtual的成员函数。它提供一种接口界面。允许在派生类种对基类的虚函数重新定义。

纯虚函数的作用：在基类中为派生类保留一个函数的名字，以便派生类根据需要对它进行定义。作为接口而存在，纯虚函数不具备函数的功能，一般不能直接被调用。

从基类继承过来的纯虚函数，在派生类中仍是虚函数。如果一个类中至少有一个纯虚函数，那么这个类被称为抽象类(不能生成对象)。

### 9. 什么是“引用”？声明和使用引用要注意哪些问题？

引用就是某个目标变量的别名(alias)，对应用的操作与对变量直接操作的效果完全相同。声明一个引用的时候，切记要对其进行初始化。引用声明完毕后，相当于目标变量有两个名称，即该目标原名称和引用名，不能再把该引用名作为其他变量名的别名。声明一个引用，不是新定义一个变量，它只表示该引用名是目标变量名的一个别名，它本身不是一中数据类型，因此引用本身不占存储单元，系统也不会给引用分配存储单元。不能建立数组的引用。

以下是一个小粒子：

	#include <iostream>
	using namespace std;

	int main(){
		int b = 5;
		int &a = b;
		cout<<a<<endl;

		b = 10;
		cout<<a<<endl;

		return 0;
	}

输出值为

	5
	10

### 11. 在什么时候需要使用“常引用”？

如果既要利用引用提高程序的效率，又要保护传递给函数的数据不在函数中被改变，就应该使用常引用。常引用声明方法：const 类型标识符 &引用名 = 目标变量名;

例如：

	int a;
	const int &ra = a;
	ra = 1;	//错误
	a = 1;	//正确

### 14. “引用”与“指针”的区别

指针通过某个指针变量指向一个对象后，对它所指向的变量间接操作。程序中使用指针，程序的可读性差；而引用本身就是目标变量的别名，对引用的操作就是对目标变量的操作。

使用引用传递函数的参数时，在内存中并没有产生实参的副本，它是直接对实参操作。使用指针作为函数的参数时也能达到与使用引用的效果，但是在被调函数中同样要给星参分配存储单元。

### 16. 结构(struct)与联合(union)有什么区别

对于联合的不同成员赋值，会对其他成员进行重写，因为union所有成员共同使用一块地址空间，而结构的不同成员使用不同地址空间。

例如：

	#include <stdio.h>
	union A{
		int i;
		char x[4];
	};

	int main(){
		A a;
		a.x[0] = 0;
		a.x[1] = 1;
		a.x[2] = 0;
		a.x[3] = 0; 

		printf("%d",a.i);
		return 0;
	}

输出为

	256

因为a中存储相当于 00000000 00000000 0000001 00000000

### 18. 关联、聚合(Aggregation)以及组合(Composition)的区别

关联是表示两个类的一般性联系，比如“学生”和“老师”就是一种关联关系；  

聚合表示has-a的关系，是一种相对松散的关系，聚合类不需要对被聚合类负责，采用空心菱形表示聚合关系，如：

	class A{...} class B{A* a; ...}

而组合表示contains-a的关系，关联性强于聚合；组合类与被组合类有相同的生命周期，组合类要对被组合类负责，采用实心菱形表示组合关系，如：

	class A{...} class B{A a; ...}

### 19. 面向对象的三大基本特征

	1. 封装：讲客观事物抽象成类，每个类对自身的数据和方法实行保护(private, protected, public)

	2. 继承：类继承和接口继承(利用纯虚函数)构成了功能复用的两种方式

	3. 多态：简单的说就是，允许将子类类型的指针赋值给父类类型的指针。

### 20. 重载(overload)和重写(override)的区别

重载：指允许存在多个同名函数，而这些函数的参数表不同(参数个数或参数类型不同)

重写：指子类重新定义父类虚函数的方法。

### 26. C++是不是类型安全的？

不是。类型安全指不能从其他对象的私有字段读取值。C++两个不同类型的指针之间可以强制转换(用reinterpret_cast)。C#是类型安全的。

### 27. main函数执行之前，还会执行什么代码

全局对象的构造函数会在main之前执行。

### 38. 如何打印当前源文件的文件名以及源文件的当前行号

	cout << __FILE__ << endl;
	cout << __LINE__ << endl;

__FILE__和__LINE__是系统预定义宏，由编译器定义的。

### 39. main主函数执行完之后，是否可能会再执行一段代码

可以，可以用atexit注册一个函数，它会在main之后执行。atexit头文件为<stdlib.h>。功能：注册终止函数(即main执行结束后调用的函数)。但是它按照LIFO(last-in-first-out)的顺序调用函数。且被调用的函数只能是不接受任何参数的void函数。例如：

	#include <stdlib.h>
	#include <stdio.h>

	void fn1( void ), fn2( void ), fn3( void ), fn4( void );

	int main( void ){
		atexit( fn1 );
		atexit( fn2 );
		atexit( fn3 );
		atexit( fn4 );
		printf( "This is executed first.\n" );
	}

	void fn1(){
	   printf( "next.\n" );
	}	

	void fn2(){
	   printf( "executed " );
	}

	void fn3(){
	   printf( "is " );
	}

	void fn4(){
	   printf( "This " );
	}

输出：

	This is executed first.
	This is executed next.

### 40. 如何判断一段程序是由C编译程序还是由C++编译程序的

在代码中加入

	#ifdef __cplusplus
	cout << "c++" <<endl;
	#else
	cout << "c" <<endl;
	#endif

### 43. 写一个函数找出一个整数数组中，第二大的数(Microsoft)

	const int MINNUMBER = -32767

	int find_sec_max(int data[], int count){
		int maxnumber = data[0];
		int sec_max = MINNUMBER;

		for(int i=0; i<count; i++){
			if(data[i] > maxnumber){
				sec_max = maxnumber;
				maxnumber = data[i];
			}
			else{
				if(data[i] > sec_max){
					sec_max = data[i];
				}
			}
		}
		return sec_max;
	}

### 47. 如何判断一个单链表是有环的(注意不能使用标记位，最多智能用两个额外指针)

使用两个指针，一个每次递增一步，一个每次递增两步，如果由环的话两者必定重合，反之亦然。

	struct node{
		char val;
		node* next;
	}

	bool check(const node* head){
		if(head==NULL)
			return false;

		node* low = head;
		node* fast = head->next;
		while(fast!=NULL && fast->next!=NULL){
			low = low->next;
			fast = fast->next->next;
			if(low==fast)
				return true;
		}
		return false;
	}
