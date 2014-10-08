---
layout: post
title:  "《剑指Offer》读书笔记"
date:   2014-10-04 12:00:00
tags:	tech
myTag:	interview

---

# 《剑指Offer》读书笔记

-----------------------------

花了几天时间看了《剑指Offer》这本书。感觉还是很不错的，适合需要校招的同学看看。然后整理了书中自己觉得需要掌握的一些面试题，一共有19题。

### 1. 定义一个空的类型，里面没有任何成员变量和成员函数。对该类型求sizeof，得到的结果是多少？

答案是`1`。空类型的实例中不包含任何信息，本来求sizeof应该是0，但是当我们声明该类型的实例的时候，它必须在内存中占有一定的空间，否则无法使用这些实例。至于占用多少内存，由编译器决定。Visual Studio 和 g++ 中每个空类型的实例占用1字节的空间。

如果在该类型中添加一个构造函数和析构函数，再对该类型求sizeof，得到的结构还是1。调用构造函数和析构函数只需要知道函数的地址即可，而这些函数的地址只与类型相关，而与类型的实例无关，编译器也不会因为这两个函数而在实例内添加任何额外的信息。

如果把析构函数标记为虚函数，那么C++的编译器一旦发现一个类型中有虚拟函数，就会为该类型生成虚函数表，并在该类型的每一个实例中添加一个指向虚函数表的指针。在32位机器上，一个指针占4个字节，因此求sizeof得到4；如果在64位机器，一个指针占8个字节，因此求sizeof得到8。

### 2. 以下程序的运行结果是 A.编译错误 B.编译成功，运行时程序崩溃 C.编译运行正常，输出10。

	class A{
	private:
		int value;
	
	public:
		A(int n){	value = n; }
		A(A other){	value = other.value; }

		void Print() {	std::cout << value << std::endl; }
	};

	int _tmain(int argc, _TCHAR* argv[]){
		A a = 10;
		A b = a;
		b.Print();
	
		return 0;
	}

拷贝构造函数A(A other)传入的参数时A的一个实例。由于是传值参数，我们把行参拷贝到实参会调用拷贝构造函数。因此如果允许拷贝构造函数传值，就会在拷贝构造函数内调用拷贝构造函数，形成无止境的递归调用导致栈溢出。因此C++的标准中不允许拷贝构造函数传值参数，在VS和GCC中，编译都会出错。解决这个问题，我们可以把构造函数修改位A(const A &other)，也就是把传值参数改为常量引用。

拷贝构造函数在3中情况下会被调用。(1)对象以值传递的方式传入函数参数。(2)对象以值传递的方式从函数返回。(3) 对象需要通过另外一个对象进行初始化。

### 3. (赋值运算符函数) 如下为类型CMyString的声明，请为该类型天津赋值运算符函数。

	class CMyString{
	public:
		CMyString(char * pData = NULL);
		CMyString(const CMyString &str);
		~CMyString(void);
	
	private:
		char *m_pData;
	};

需要考虑以下几点:

1. 是否把返回值的类型声明为该类型的引用，并在函数解释前返回实例自身的引用(\*this)。只有返回引用才可以连续赋值。

2. 是否把传入的参数的类型声明为常量引用。如果不是，那么从行参到实参会调用一次拷贝构造函数，产生了无谓的消耗。

3. 是否释放实例自身已有的内存。如果我们在分配信内存之前忘记释放自身已有的空间，程序将会出现内存泄露。

4. 是否判断传入的参数和当前的实例(\*this)是不是同一个实例。如果是同一个，则不进行赋值操作，直接返回。如果不这么做，那么在释放实例自身内存的时候会导致传入参数的内存同时被释放掉，而找不到需要赋值的内容。

	CMyString& CMyString::operator =(const CMyString &str){
		if(this == &str)
			return *this;

		delete []m_pData;
		m_pData = NULL;

		m_pData = new char[strlen(str.m_pData) + 1];
		strcpy(m_pData, str.m_pData);

		return *this;
	}


### 4. (二维数组中的查找) 在一个二维数组中，每一行都按照从左到右的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。例如下面的二维数组，如果在这个数组中查找数字7，则返回true；如果查找数字5，由于数组不含有该数字，则返回false。(2015百度软件开发工程师面试题)

	1	2	8	9
	2	4	9	12
	4	7	10	13
	6	8	11	15

考虑查找数字7这种情况进行分析，我们从右上角开始查找。第四列第一个值是9，由于7小于9，而二维数组是严格向下递增的，所以7不可能在9的下方。所以我们向左搜索。第三列第一个值是8，同理我们向左搜索。第二列第一个值是2，由于2的左边的值一定比2小，而2右边的列已经剔除出搜索范围，所以我们向下搜索。第二列第二个值是4，同理我们向下搜索。第二列第三个值是7，返回true。

	bool Find(int *matrix, int row, int col, int x){
		int i = 0;
		int j = col-1;

		while(matrix!=NULL && i<row && 0<=j){
			if(matrix[i*col + j] == x){
				return true;
			}
			else{
				if(matrix[i*col +j] > x)
					j--;
				else
					i++;
			}
		}
		return false;
	}

### 5. (字符串) 运行下面的代码，得到的结果是什么？

	#include <stdio.h>

	int main(){

		char str1[] = "hello world";
		char str2[] = "hello world";

		char *str3 = "hello world";
		char *str4 = "hello world";

		if(str1==str2)
			printf("str1 and str2 are the same.\n");
		else
			printf("str1 and str2 are not the same.\n");

		if(str3==str4)
			printf("str3 and str4 are the same.\n");
		else
			printf("str3 and str4 are not the same.");

		return 0;
	}

输出结果是

	str1 and str2 are not the same.
	str3 and str4 are the same.

str1和str2是两个字符串数组，我们会为它们分屏两个长度为12个字节的空间，并把"hello world\n"的内容分别复制到数组中去。这是两个初始地址不同的数组，因此str1和str2的值也不相同，所以输出的第一行是"str1 and str2 are not the same"。

str3和str4是两个指针，我们无需为它们分配内存以存储字符串的内容，而只需要把他们指向"hello world"在内存中的地址就可以了。由于"hello world"是常量字符串，他在内存中只有一个拷贝，因此str3和str4指向的是同一个地址。所以第二行的输出是"str3 and str4 are the same"。

### 6. (替换空格) 请实现一个函数，把字符串中的每个空格替换成"%20"。例如输入"We are happy."，则输出"We%20are%20happy."

我们应该首先想到替换后字符串会变长。如果需要在原来的字符串上做替换，那么就有可能覆盖修改字符串后面的内存。如果是创建新字符串，那么我们可以自己分配足够多的内存。我们需要首先明确题目需求。假设我们需要在原字符串上做替换，并保证输入的字符串后面有足够多的空余内存。

时间复杂度在O(n)的解法是，先遍历一次，知道有多少空格，确定字符串长度，从后往前替换。

	void ReplaceBlank(char *str){
		int blank = 0;
		char *p = NULL, *q=NULL;

		int i=0;
		while(str!=NULL && str[i]!='\0'){
			if(str[i]==' ')
				blank++;
			i++;
		}

		p = &str[i];
		q = p + 2*blank;

		while(p!=q){
			if(*p!=' '){
				*q-- = *p--;
			}
			else{
				p--;
				*q-- = '0';
				*q-- = '2';
				*q-- = '%';
			}
		}
	}

### 7. (重建二叉树) 输入某二叉树的前序遍历和中序遍历。请重建出该二叉树。假设输入的前序遍历和中序遍历的结果都不含重复的数字。例如输入得前序遍历是{1,2,4,7,3,5,6,8}，中序遍历是{4,7,2,1,5,3,8,6}，重建出二叉树并输出它得头结点。二叉树节点的定义如下:

	struct BinaryTreeNode{ 
		int m_nValue;
		BinaryTreeNode *m_pLeft;
		BinaryTreeNode *m_pRight;
	};

对于前序遍历，它的第一个值一定是根结点，紧接着的元素组成左子树，再紧接着的元素组成右子树。对于中序遍历，根结点位置的左边元素组成左子树，根结点位置的右边元素组成右子树。于是我们现在知道了左右子树各自的前序和中序遍历，我们可以递归的重建二叉树。

BinaryTreeNode * constructTree(int *pre, int *mid, int length){
	BinaryTreeNode *root = NULL;

	if(length<=0 || pre==NULL || mid==NULL )
		return root;

	int i = 0;
	while(mid[i]!=pre[0] && i<length){
		i++;
	}

	root = new BinaryTreeNode();
	root->m_nValue = pre[0];
	root->m_pLeft = constructTree(&pre[1], &mid[0], i);
	root->m_pRight = constructTree(&pre[i+1], &mid[i+1], length-i-1);
}

### 8. (用两个栈实现队列) 用两个栈实现一个队列。队列的声明如下，请实现它的两个函数appendTail和deleteHead，分别完成在队列尾部插入结点和在队列头部删除结点的功能。

	template <typename T> class CQueue{
	public:
		CQueue(void);
		~CQueue(void);

		void appendTail(const T& node);
		T deleteHead();

	private:
		stack<T> stack1;
		stack<T> stack2;
	};

如果你想着维护一个栈有序，另一个栈用来做临时存储，那么这样不是最优的。最优的方法是，当需要appendTail我们就加入stack1，当需要deleteHead的时候，查看stack2是不是空，如果为空则将stack1的元素依次弹出并压入stack2中，删除stack2栈顶元素；如果stack2不为空，那么直接删除stack2栈顶元素。


	template<typename T> void CQueue<T>::appendTail(const T& node){
		stack.push(node);
	}

	template<typename T> T CQueue<T>::deleteHead(){
		if(stack2.empty()){
			while(!stack.empty()){
				stack2.push(stack1.top());
				stack1.pop();
			}
		}

		T tmp = stack2.top();
		stack2.pop();
		return tmp;
	}

### 9. (斐波那契数列) 我们可以用2x1的小矩形横着或者竖着去覆盖更大的矩形。请问8个2x1的小矩形无重复地覆盖一个2x8的大矩形总共有多少种方法？

	+-+		+-+-+-+-+-+-+-+-+
	+-+		+-+-+-+-+-+-+-+-+
	+-+		+-+-+-+-+-+-+-+-+

假设我们将求2x8的矩形的摆法设为f(8)，当将一个2x1矩形竖着放在最左边之后，剩下的摆法有f(7)，如果我们将一个2x1矩阵横着放在左上角，那么左下角也需要放一个2x1的矩形，剩下的摆法有f(6)，所以f(n)=f(n-1)+f(n-2)，这是一道斐波那契数列的变形题。

### 10. (数值的整数次方) 实现函数double Power(double base, int exponent)，求base的exponent次方。不得使用库函数，同时不需要考虑大数问题。

这道题目需要考察的是面试者有没有考虑全面。如果exponent是不是正数或者base是0的情况该怎么办。

	bool InvalidInput = false;

	bool equal(double a, double b){
		if(a-b < 0.000001 && a-b > -0.000001)
			return true;
		else
			return false;
	}

	double Power(double base, int exponent){
		if(equal(base,0,0) && exponent<=0){
			InvalidInput = true;
			return 0.0;
		}

		double result = 1.0;

		if(exponent>0){
			while(exponent--)
				result*=base;
			return result;
		}
		else if(exponent==0){
			return result;
		}
		else if{exponent<0}{
			while(exponent++)
				result*=base;
			return 1.0 / result;
		}
	}

定义一个全局变量InvalidInput作为0的0次方的错误标记。

### 11. (在O(1)时间删除链表结点) 给定单向链表的头指针和一个结点指针，定义一个函数在O(1)时间删除该结点。链表结点与函数的定义如下:

	struct ListNode{
		int m_nValue;
		LisstNode* m_pNext;
	}

	void DeleteNode(ListNode** pListHead, ListNode* pToBeDeleted);

单向链表删除一个结点，最常规的做法是从链表头结点开始，顺序遍历找到要删除的结点，并在链表中删除该结点。时间复杂度是O(n)。

更优的方法是将当前要删除结点的下一个结点的值赋给当前结点，然后删除本来要删除的下一个结点。这种方法不用遍历链表，时间复杂度为O(1)。需要注意的是如果要删除的结点为最后一个结点，它并没有下一个结点，我们只好顺序遍历得到该结点的前序结点，并完成删除操作。

	void DeleteNode(ListNode** pListHead, ListNode* pToBeDeleted){
	
		if(!pListHead || !pToBeDeleted)
			return;

		if(pToBeDeleted->m_pNext != NULL){
			ListNode* pNext = pToBeDeleted->m_pNext;
			pToBeDeleted->m_nValue = pNext->m_nValue;
			pToBeDeleted->m_pNext = pNext->m_pNext;

			delete pNext;
			pNext = NULL;
		}
		else if(*pListHead == pToBeDeleted){
			delete pToBeDeleted;
			pToBeDeleted = NULL;
			*pListHead = NULL;
		}
		else{
			ListNode* pNode = *pListHead;
			while(pNode->m_pNext != pToBeDeleted)
				pNode = pNode->m_pNext;
			pNode->m_pNext = NULL;
			delete pToBeDeleted;
			pToBeDeleted = NULL;
		}
	}

### 12. (包含min函数的栈) 定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的min函数。在该栈中，调用min、push及pop的时间复杂度都是O(1)。(2015百度软件开发工程师笔试题)

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

### 13. (复杂链表的复制) 请实现函数ComplexListNode* Clone(ComplexListNode* pHead)，复制一个复杂链表。在复杂链表中，每个结点除了有一个m\_pNext指针指向下一个结点外，还有一个m\_pSibing指向链表中的任意结点或者NULL。结点的C++定义如下:

	struct ComplexListNode{
		int					m_nValue;
		ComplexListNode*	m_pNext;
		ComplexListNode*	m_pSibling;
	};

例如:

				+-------------------+
				V					|
	+---+	  +---+	    +---+	  +-+-+	    +---+	  
	| A | --> | B | --> | C | --> | D | --> | E | 
	+-+-+	  +-+-+	    +---+	  +---+	    +---+	
	  |			|		  A					  A
	  |			+---------|-------------------+
	  +-------------------+

本题的难点是需要如何进行复制，因为m\_pSibing如果指向后面的结点，但是后面的结点我们还没有新建，那么就没有办法指向。所以问题可以分成两部分，第一部分是构建用m\_pNext连起来的链表，第二部分是遍历原来的链表将新链表的m\_pSibing进行赋值。但是如果直接做，每次都需要从头指针遍历一次，时间复杂度是O(n^2)。思考有没有一种方法可以在O(n)的时间复杂度上做m\_pSibing的赋值。答案是有的，如果我们将赋值后的结点称为A', B', ...，每一个新结点都首先链接在旧结点后面 ：A->A'->B->B'->...->E->E'，那么此时，我们只需要遍历一次链表就可以直接将新结点的m\_pSibing赋值，最后再将两个链表分开即可。

	struct ComplexListNode{
		int                 m_nValue;
		ComplexListNode*    m_pNext;
		ComplexListNode*    m_pSibling;
	};

	ComplexListNode* Clone(ComplexListNode* pHead){
		ComplexListNode *p = pHead;
		while(p!=NULL){
			ComplexListNode* newNode = new ComplexListNode();
			newNode->m_nValue = p->m_nValue;
			newNode->m_pNext = p->m_pNext;
			newNode->m_pSibling = NULL;

			p->m_pNext = newNode;
			p = newNode->m_pNext;
		}

		p = pHead;
		while(p!=NULL){
			if(p->m_pSibling!=NULL)
				p->m_pNext->m_pSibling = p->m_pSibling->m_pNext;
			p = p->m_pNext->m_pNext;
		}

		p = pHead;
		ComplexListNode* pNewHead = NULL;
		ComplexListNode* q = NULL;

		if(p!=NULL){
			pNewHead = p->m_pNext;
		}
		while(p!=NULL){
			q = p->m_pNext;
			p->m_pNext = q->m_pNext;

			if(p->m_pNext!=NULL)
				q->m_pNext = p->m_pNext->m_pNext;

			p = p->m_pNext;
		}

		return pNewHead;
	}	

### 14. (二叉搜索树与双向链表) 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。比如下图

			  +----+
			  | 10 |
			  +----+
			 /		\
		+---+		 +----+				
		| 6 |	     | 14 |			
		+---+    	 +----+
	   /     \		/	   \
	+---+  +---+  +----+  +----+
	| 4 |  | 8 |  | 12 |  | 16 |
	+---+  +---+  +----+  +----+
	
	4 <=> 6 <=> 8 <=> 10 <=> 12 <=> 14 <=> 16

二叉树结点定义如下

	struct BinaryTreeNode{
		int				m_nValue;
		BinaryTreeNode*	m_pLeft;
		BinaryTreeNode* m_pRight;
	};


代码如下:

	BinaryTreeNode* Convert(BinaryTreeNode* pRootOfTree){

		BinaryTreeNode *pLastNodeInList = NULL;
		ConvertNode(pRootOfTree, &pLastNodeInList);

		BinaryTreeNode *pHeadOfList = pLastNodeInList;
		while(pHeadOfList != NULL && pHeadOfList->m_pLeft != NULL)
			pHeadOfList = pHeadOfList->m_pLeft;

		return pHeadOfList;
	}

	void ConvertNode(BinaryTreeNode* pNode, BinaryTreeNode** pLastNodeInList){
		
		if(pNode == NULL)
			return ;

		BinaryTreeNode *pCurrent = pNode;

		if(pCurrent->m_pLeft != NULL)
			ConvertNode(pCurrent->m_pLeft, pLastNodeInList);

		pCurrent->m_pLeft = *pLastNodeInList;
		if(*pLastNodeInList != NULL)
			(*pLastNodeInList)->m_pRight = pCurrent;

		*pLastNodeInList = pCurrent;

		if(pCurrent->m_pRight != NULL)
			ConvertNode(pCurrent->m_pRight, pLastNodeInList);
	}

### 15. (字符串的排列) 输入一个字符串，打印出该字符串字符的所有排列。例如输入字符串abc，则打印出a、b、c所能排列出来的所有字符串abc、acb、bac、bca、cab和cba。

我们这样思考，如果将第一个字母和后面所有字母的位置做交换，我们能得到这样一个组字符串:

	'a'bcd...
	'b'acd...
	'c'bad...
	'd'bca...

然后对于每一组字符串，我们可以考虑将第二个字符和后面所有字母的位置进行交换。这样现在是一个递归的思路。

	void permutation(char* str){
		if(str==NULL)
			return;
		
		Permutation(str, str);
	}

	void Permutation(char* str, char* begin){
		if(*begin == '\0')
			printf("%s\n", str);
		else{
			for(char* ch = begin; *ch!='\0'; ch++){
				char tmp = *ch;
				*ch = *begin;
				*begin = tmp;

				Permutation(str, begin+1);

				tmp = *ch;
				*ch = *begin;
				*begin = tmp;
			}
		}
	}

### 16. (数组中出现次数超过一半的数字) 数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字在数组中出现了5次，超过数组长度的一半，所以输出2。

如果数组是有序的，那么中位数一定是我们要求的值。但是排序的时间复杂度为O(nlogn)，仅仅使用排序，没有用到"数字出现次数超过数组长度的一半"这个属性。思考，如果我们选择一个数，将比它大的数放到它的右边，比它小的数放到它的左边，递归这样操作，最终位于数组中间的数的值就是我们要求的值。

还有另一种解法，我们遍历数组，同时记录两个值，一个是当前数字，还有一个是当前数字出现的次数。如果下一个数组的值和当前记录数字的值一致，则将次数+1，否则-1。如果次数为0，则我们需要保存下一个数字。因为所求数字出现的次数超过数组长度的一半，所以最终保留的一定是所求值。

	int MoreThanHalfNum(int* numbers, int length){
		
		if(CheckInvalidArray(numbers, length))
			return 0;

		int result = numbers[0];
		int times = 1;
		for(int i=1; i<length; i++){
			if(times==0){
				result = numbers[i];
				times = 1;
			}
			else if(numbers[i] == result)
				times++;
			else
				times--;
		}

		if(!CheckMoreThanHalf(numbers, length, result))
			result = 0;

		return result;
	}

其中CheckInvalidArray()和CheckMoreThanHalf()是判断输入的数组是不是合法的。

### 17. (从1到n整数中1出现的次数) 输入一个整数n，求从1到n这n个整数的十进制表示中1出现的次数。例如输入12，从1到12这些整数中包含1的数字有1，10，11和12，1一种出现了5次。

考虑一个大一点的数abcde，如果百位上的数字为0，那么百位上出现1的次数由更高位决定，例如12013，百位出现1的情况共有12x100=1200种。如果百位上的数字为1，百位上出现1的情况由更高位和更低位共同决定，例如12113，百位上出现1的次数为12x100+13+1=1214。如果百位上的数字为2~9，那么百位上1出现的次数的情况由更高位+1决定，例如12213，百位上为1的情况共有(12+1)x100=1300种。同理我们可以推导其他位置。


	int NumberOf1Between1AndN(unsigned int n){
		int number = 0;

		unsigned int filter = 1;

		if(n==0)
			return 0;

		while(filter <= n){
			unsigned int upper = n / (filter*10);
			unsigned int lower = n % filter;
			unsigned int current = n / filter % 10; 

			if(current==0)
				number+=(upper*filter);
			else if(current==1)
				number+=(upper*filter + lower+1);
			else
				number+=(upper+1)*filter;

			filter*=10;
		}

		return number;
	}

### 18. (把数组排成最小的数) 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3,32,321}，则打印出3个数字能排成的最小数字321323。

根据题目要求，两个m和n能拼成数字mn和nm。如果mn&lt;nm，则m排在n前面。我们对输入的数字按照这一规则排序。

	#include <iostream>
	#include <string>
	#include <algorithm>
	using namespace std;

	bool cmp(int a, int b){
		string A = "";
		string B = "";
		while(a){
			A = char((a%10)+'0') + A;
			a/=10;
		}
		while(b){
			B = char(b%10+'0') + B;
			b/=10;
		}

		return A+B < B+A;
	}

	void MinNumber(int *array, int length){
		sort(array, array+length, cmp);
		for(int i=0; i<length; i++)
			cout<<array[i];
		cout<<endl;
	}

### 19. (不用加减乘除做加法) 写一个函数，求两个整数之和，要求在函数体内不得使用加减乘除四则运算符。

如果不能使用加减乘除四则运算符，那么能用的只剩下位运算了。思考两个数相加在计算机内的实现过程。二进制0+0=0，0+1=1，1+0=0，1+1=0(进位)。如果我们首先不考虑进位，那么两个数相加，相当于两个数异或。如何实现进位呢？只有1+1会产生进位，相当于做与操作，于是进位相当于与操作并且左移一位。

	int Add(int num1, int num2){
		int sum, carry;
		do{
			sum = num1^num2;
			carry = (num1 & num2) << 1;

			num1 = sum;
			num2 = carry;
		}
		while(num2!=0);

		return num1;
	}
