---
layout: post
title:  "2015 百度 软件研发工程师 笔试题"
date:   2014-09-21 12:00:00
tags:	tech
myTag:	interview

---

# 2015 百度 软件研发工程师 笔试题

-----------------------------

## 一 简答题

### 1. 进程与线程之间的区别与联系。

1. 进程是资源的拥有者，线程是资源的执行者。
2. 一个线程只能属于一个进程，而一个进程可以有多个线程，但至少有一个线程。
3. 资源分配给进程，同一进程的所有线程共享该进程的所有资源。 同一进程中的多个线程共享代码段(代码和常量)，数据段(全局变量和静态变量)，扩展段(堆存储)。但是每个线程拥有自己的栈段，栈段又叫运行时段，用来存放所有局部变量和临时变量。

[(进程与线程的区别和联系)](http://blog.sina.com.cn/s/blog_a2c4494a010120x0.html)

### 2. 阐述数据库的流程以及优点


### 3. static全局变量和普通全局变量有什么不同? static局部变量和普通局部变量有什么不同? static函数和普通函数有什么不同?

1. static用于全局变量，主要作用是限制此全局变量被其他的文件调用。静态全局变量全局变量(外部变量)的说明之前再冠以static 就构成了静态的全局变量。全局变量本身就是静态存储方式，静态全局变量当然也是静态存储方式。 这两者在存储方式上并无不同。这两者的区别虽在于非静态全局变量的作用域是整个源程序，当一个源程序由多个源文件组成时，非静态的全局变量在各个源文件中都是有效的。 而静态全局变量则限制了其作用域， 即只在定义该变量的源文件内有效，在同一源程序的其它源文件中不能使用它。由于静态全局变量的作用域局限于一个源文件内，只能为该源文件内的函数公用，因此可以避免在其它源文件中引起错误。从以上分析可以看出，把局部变量改变为静态变量后是改变了它的存储方式即改变了它的生存期。把全局变量改变为静态变量后是改变了它的作用域，限制了它的使用范围。因此static 这个说明符在不同的地方所起的作用是不同的。
2. 用于局部变量中，成为静态局部变量。静态局部变量有两个用法，记忆功能和全局生存期。
	+ 静态局部变量在函数内定义 它的生存期为整个源程序，但是其作用域仍与自动变量相同，只能在定义该变量的函数内使用该变量。退出该函数后， 尽管该变量还继续存在，但不能使用它。
	+ 允许对构造类静态局部量赋初值 例如数组，若未赋以初值，则由系统自动赋以0值。
	+ 对基本类型的静态局部变量若在说明时未赋以初值，则系统自动赋予0值。而对自动变量不赋初值，则其值是不定的。 根据静态局部变量的特点，可以看出它是一种生存期为整个源程序的量。虽然离开定义它的函数后不能使用，但如再次调用定义它的函数时，它又可继续使用，而且保存了前次被调用后留下的值。因此，当多次调用一个函数且要求在调用之间保留某些变量的值时，可考虑采用静态局部变量。虽然用全局变量也可以达到上述目的，但全局变量有时会造成意外的副作用，因此仍以采用局部静态变量为宜。
3. static函数 —— 内部函数和外部函数 当一个源程序由多个源文件组成时，Ｃ语言根据函数能否被其它源文件中的函数调用，将函数分为内部函数和外部函数。
	+ 内部函数（又称静态函数）如果在一个源文件中定义的函数，只能被本文件中的函数调用，而不能被同一程序其它文件中的函数调用，这种函数称为内部函数。定义一个内部函数，只需在函数类型前再加一个“static”关键字即可，如下所示： static 函数类型 函数名(函数参数表) {……} 关键字“static”，译成中文就是“静态的”，所以内部函数又称静态函数。但此处“static”的含义不是指存储方式，而是指对函数的作用域仅局限于本文件。使用内部函数的好处是：不同的人编写不同的函数时，不用担心自己定义的函数，是否会与其它文件中的函数同名，因为同名也没有关系。
	+ 外部函数外部函数的定义：在定义函数时，如果没有加关键字“static”，或冠以关键字“extern”，表示此函数是外部函数。

[(static 变量 全局变量)](http://blog.csdn.net/virtualdesk/article/details/4394563)

## 二 程序设计题

### 1. 编写函数void* memcpy(void* dst, void* src, unsigned int len);

memcpy需要考虑地址重叠的问题。

	void *memcpy(void *dst, void *src, size_t size){
		char *psrc;
		char *pdst;
	 
		if(NULL == dst || NULL == src){
			return NULL;
		}
	 
		if((src < dst) && (char *)src + size > (char *)dst){ // 自后向前拷贝
			psrc = (char *)src + size - 1;
			pdst = (char *)dst + size - 1;
			while(size--){
				*pdst-- = *psrc--;
			}
		}
		else{
			psrc = (char *)src;
			pdst = (char *)dst;
			while(size--){
				*pdst++ = *psrc++;
			}
		}

		return dst;
	}

[(实现memcpy函数)](http://my.oschina.net/renhc/blog/36345)

### 2. 对于一个N*N的矩阵A(N<10000)，有如果i&lt;j，则(1)A[i][n]&lt;A[j][n]; (2)A[n][i]&lt;A[n][j]; 编写一个函数，当输入一个值M，输出M对应的位置例如\<2,1\>，如果没有则输出NULL。(请注意效率)


	void find(int M){
		int i=0;
		int j=0;

		while(true){
			//	find in col
			while(i-1>=0 && A[i][j]>M)
				i--;
			while(i+1<N && A[i][j]<M)
				i++;
			if(A[i][j]==M)
				break;

			// go to next col
			if(j+1<N)
				j++;
			else
				break;
		}

		if(A[i][j]==M)
			cout << "<"<<i<<","<<j<<">"<<endl;
		else
			cout << "NULL" << endl;
	}

其实这个设计也不好，我们可以从右上角的元素开始判断。如果所查找的元素比当前元素小，则向左搜索；如果查找的元素比当前元素大，则向下搜索。


	bool Find(int *matrix, int row, int col, int x){
		int i = 0;
		int j = col-1;

		while(matrix!=NULL && i<row && 0<=j){
			if(matrix[i*col + j] == x){
				cout << '<' << i << " " << j << endl;
				return true;
			}
			else{
				if(matrix[i*col +j] > x)
					j--;
				else
					i++;
			}
		}
		cout << "NULL" << endl;
		return false;
	}


### 3. 实现一个栈，具有min函数，且时间复杂度为O(1)。

我们需要记住的是这个新的数据结构依然要保持后进先出的栈的特性，所以如果你打算利用两个栈将元素移动，每次返回min，那么可能是行不通的。

如果我们用另一个辅助栈，来记录当前最小元素的值，每次添加元素的时候，这个辅助栈就记录当前最小的元素；每次弹出元素的时候，这个辅助栈就弹出当前最小的元素。例如:

	步骤		数据栈		辅助栈		最小值		返回值
	push(3)		3			3			3
	push(4)		3,4			3,3			3
	push(2)		3,4,2		3,3,2		2
	push(1)		3,4,2,1		3,3,2,1		1
	pop()		3,4,2		3,3,2		2			1
	pop()		3,4			3,3			3			2
	push(0)		3,4,0		3,3,0		0

代码如下

	class StackWithMin{
	private:
		stack<int> data_stack;
		stack<int> min_stack;

	public:
		void push(int x){
			data_stack.push(x);
			if(min_stack.empty() || min_stack.top() > x)
				min_stack.push(x);
			else
				min_stack.push(min_stack.top());
		}

		int pop(){
			int tmp = data_stack.top();
			data_stack.pop();
			min_stack.pop();
			return tmp;
		}

		int min(){
			return min_stack.top();
		}
	};




## 三 系统设计题

### 1. 微博的限定字数是140个字，但是通常一个链接(eg: http://zhidao.baidu.com/question/488691941257880332.html)太长，会导致可写字数减少。现在通常都会将一个URL映射到一个短的tinyURL上(eg: http://tinyurl.com/mtfgubv)。其中"tinyurl.com"是服务器的前缀，请你设计将URL映射到tinyURL的方法。

参考本人另一篇文章[URL Shortening](http://edlin.me/URL_Shortening.html)

### 2. 给定一个URL如何查找一个已经存在的tinyURL。

参考本人另一篇文章[URL Shortening](http://edlin.me/URL_Shortening.html)

### 3. 如果现在有十亿条URL，需要分布在10台服务器，如何进行处理。


