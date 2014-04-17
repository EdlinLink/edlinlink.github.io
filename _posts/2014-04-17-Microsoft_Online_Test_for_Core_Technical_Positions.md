---
layout: post
title:  "微软2014实习生及秋令营技术类职位在线测试"
date:   2014-04-17 12:00:00
tags:	tech
myTag:	

---

# 微软2014实习生及秋令营技术类职位在线测试

-------------------------------------------

### 题目1: String reorder

时间限制:10000ms  
单点时限:1000ms  
内存限制:256MB  

### Description

For this question, your program is required to process an input string containing only ASCII characters between ‘0’ and ‘9’, or between ‘a’ and ‘z’ (including ‘0’, ‘9’, ‘a’, ‘z’).

Your program should reorder and split all input string characters into multiple segments, and output all segments as one concatenated string. The following requirements should also be met,

1\. Characters in each segment should be in strictly increasing order. For ordering, ‘9’ is larger than ‘0’, ‘a’ is larger than ‘9’, and ‘z’ is larger than ‘a’ (basically following ASCII character order).

2\. Characters in the second segment must be the same as or a subset of the first segment; and every following segment must be the same as or a subset of its previous segment.

Your program should output string “<invalid input string>” when the input contains any invalid characters (i.e., outside the '0'-'9' and 'a'-'z' range).

### Input

Input consists of multiple cases, one case per line. Each case is one string consisting of ASCII characters.

### Output

For each case, print exactly one line with the reordered string based on the criteria above.

### 样例输入

	aabbccdd
	007799aabbccddeeff113355zz
	1234.89898
	abcdefabcdefabcdefaaaaaaaaaaaaaabbbbbbbddddddee

### 样例输出

	abcdabcd
	013579abcdefz013579abcdefz
	<invalid input string>
	abcdefabcdefabcdefabdeabdeabdabdabdabdabaaaaaaa


### My Thoughts

既然每一段的字符都是要严格递增的，且后面一段必须与前一段相通或者为前一段的子集。简单的思路就是统计好所有字符的个数，然后将所有字符每次全部取一个，作为一段，直到最终没有字符。

事实上，这一题的思路很正确，但是我只拿了90分。根据开源中国网友[jinjiao00](http://www.oschina.net/code/snippet_928691_35005)的提示。原题的输入串可能是空串或者是只包含空格的串，而cin>>会自动过滤掉。应该使用cin.getline等。这里我建议用getline。

### My Code

``` c++

#include <iostream>
#include <string>
#include <cstring>
using namespace std;

int main(){

	//ascii '0'=48, 'z'=122, totally less than 75.
	int cha[75];
	string str;

	while(getline(cin,str)){
		bool valid = true;
		memset(cha, 0, sizeof(cha));

		int len = str.length();
		for(int i=0; i<len; i++){
			if(str[i]<'0' || (str[i]>'9' and str[i]<'a') || str[i]>'z')
				valid = false;
			else
				cha[str[i]-'0']++;
			
			if(!valid)
				break;
		}



		if(!valid){
			cout<<"<invalid input string>"<<endl;
		}
		else{
			bool zero = true;
			while(zero){
				for(int i=0; i<75; i++){
					if(cha[i]-- > 0){
						cout<<(char)(i+'0');
						zero = false;
					}
				}

				if(zero){
					cout<<endl;
					break;
				}
				else{
					zero = true;
				}
			}
		}
	}

	return 0;
}


```

----------------------------------------------------------------------------
----------------------------------------------------------------------------

### 题目2: K-th string

时间限制:10000ms  
单点时限:1000ms  
内存限制:256MB  

### Description

Consider a string set that each of them consists of {0, 1} only. All strings in the set have the same number of 0s and 1s. Write a program to find and output the K-th string according to the dictionary order. If such a string doesn’t exist, or the input is not valid, please output “Impossible”. For example, if we have two ‘0’s and two ‘1’s, we will have a set with 6 different strings, {0011, 0101, 0110, 1001, 1010, 1100}, and the 4th string is 1001.

### Input

The first line of the input file contains a single integer t (1 ≤ t ≤ 10000), the number of test cases, followed by the input data for each test case.
Each test case is 3 integers separated by blank space: N, M(2 <= N + M <= 33 and N , M >= 0), K(1 <= K <= 1000000000). N stands for the number of ‘0’s, M stands for the number of ‘1’s, and K stands for the K-th of string in the set that needs to be printed as output.

### Output

For each case, print exactly one line. If the string exists, please print it, otherwise print “Impossible”.

### 样例输入

	3
	2 2 2
	2 2 7
	4 7 47

### 样例输出

	0101
	Impossible
	01010111011

### My Thoughts

这一题其实一开始没有什么思路，如果没有思路的话，那就从只有1个1，1个0的情况开始找规律。突然，就找到规律了。这个题目主要分两部分，一部分是要知道究竟有多少种不同的排列，另外一个就是要知道排列的第几个数是多少。现在我们先解决第一部分。

我们将题目变成一道高中排列组合的题目，我们用样例3作为例子: 有11(4+7)个数字排成一排，任意挑选其中的4个为0（即剩下的都为1），共有多少种组合？答案就是C(11,4) = (11*10*9*8)/(1*2*3*4) = 330。同理，2个0，2个1共有C(4,2)=6种组合。

现在我们知道了组合的个数，我们需要解决排列的第几个数是什么。依然使用样例3为例子。我们这里设计一个函数CalTotal(N,M)来计算N个0和M个1总共的组合方式。现在有4个0和7个1，要求第47个数是什么。4个0和7个1能组成的数字空间共有CalTotal(4,7)=330，第47个数显然不会超出这个数字空间。考虑第一个数，假设为0，那么后面能使用的数字剩下3个0和7个1，CalTotal(3,7)=120，且这120个数是排在330个数的前面120个，显然第47个数也在这120个数里面，所以我们能够确定第一位为0。

在我们确定第一位为0之后，现在的问题转化成了有3个0和7个1，求第47个数是什么？假设我们再次认为这个10位数的第一位为0（即原来的11位数第二位为0）那么CalTotal(2,7)=36，小于47，也即是第一位必须为1。那么现在确定第二位为1之后，题目转化成有3个1和6个1，求第11（47-36）个数是什么？47-36是因为我们知道从00xxxxxxxxx的最小数到01xxxxxxxxxx的最小数，中间一共有36种情况，所以求xxxxxxxxxxx的第47个数，也即是求0xxxxxxxxxx作为起始开始数的第47个数，即是求01xxxxxxxxx作为起始开始数的第11个数。

现在我们重新看这道题目，现在我们用K表示要求的第K个数，K=47，N、M分别表示0和1的剩余个数:  

	考虑第一位为0，剩余组合CalTotal(3,7)=330，K<330，第一位确定为0，K=47，N=3，M=7；  
	考虑第二位为0，剩余组合CalTotal(2,7)=36，K>36，第二位确定为1，K=47-36=11，N=3，M=6；  
	考虑第三位为0，剩余组合CalTotal(2,6)=28，K<28，第三位确定为0，K=11，N=2，M=6；  
	考虑第四位为0，剩余组合CalTotal(1,6)=7，K>7，第四位确定为1，K=11-7=4，N=2，M=5；  
	考虑第五位为0，剩余组合CalTotal(1,5)=6，K<6，第五位确定为0，K=4，N=1，M=5；  
	考虑第六位为0，剩余组合CalTotal(0,5)=1，K>1，第六位确定为1，K=4-1=3，N=1，M=4；  
	考虑第七位为0，剩余组合CalTotal(0,4)=1，K>1，第七位确定为1，K=3-1=2，N=1，M=3；  
	考虑第八位为0，剩余组合CalTotal(0,3)=1，K>1，第八位确定为1，K=2-1=1，N=1，M=2；  
	考虑第九位为0，剩余组合CalTotal(0,2)=1，K=1；第九位确定为0，K=1-1=0，N=0，M=2； 

当考虑到第九位数的时候，K的值为0，此时还未确定的数字有0个0和2个1，我们只要将剩余数字按最大的数排列即可（即先排1再排0），本例子中，只剩下两个1没有0了，所以接下去第10和第11为皆为1。最终我们知道4个0和7个1的第47个数为01010111011。

记得特殊处理一下N、M分别为0的情况即可。

### My Code

``` c++

#include <iostream>
using namespace std;


long CalTotal(long a, long b){
	if(a==0 && b==0)
		return 0;
	else if(a*b==0)
		return 1;

	if(a<b){
		int c = a; 
		a = b; 
		b = c;
	}
	long sum = a+b;
	long total = 1;
	for(int i=0; i<b; i++){
		total*=sum;
		sum--;
	}
	for(int i=1; i<=b; i++){
		total/=i;
	}

	return total;
}


int main(){

	int tc;
	cin >> tc;

	while(tc--){
		long  N, M, K;
		cin >> N >> M >> K;

		long total = CalTotal(N, M);
		
		if(K>total){
			cout<<"Impossible"<<endl;
		}
		else if(K==total){
			for(int i=0;i<M; i++)	cout<<"1";
			for(int i=0;i<N; i++)	cout<<"0";
			cout<<endl;
		}
		else{
			while(K!=0){
			
				long temp = CalTotal(N-1, M);
				if(K<temp){
					cout<<"0";
					N--;
				}
				else if(K>temp){
					cout<<"1";
					M--;
					K-=temp;
				}
				else{
					cout<<"0";
					N--;
					K-=temp;
				}
			}

			for(int i=0;i<M; i++)	cout<<"1";
			for(int i=0;i<N; i++)	cout<<"0";
			cout<<endl;
		}
	}

	return 0;
}


```

---------------------------------------------------------------------------
---------------------------------------------------------------------------

### 题目3: Reduce inversion count

时间限制:10000ms  
单点时限:1000ms  
内存限制:256MB  

### Description

Find a pair in an integer array that swapping them would maximally decrease the inversion count of the array. If such a pair exists, return the new inversion count; otherwise returns the original inversion count.

Definition of Inversion: Let (A[0], A[1] ... A[n], n <= 50) be a sequence of n numbers. If i < j and A[i] > A[j], then the pair (i, j) is called inversion of A.

Example:  
Count(Inversion({3, 1, 2})) = Count({3, 1}, {3, 2}) = 2  
InversionCountOfSwap({3, 1, 2})=>  
{  
	InversionCount({1, 3, 2}) = 1 <-- swapping 1 with 3, decreases inversion count by 1  
	InversionCount({2, 1, 3}) = 1 <-- swapping 2 with 3, decreases inversion count by 1  
	InversionCount({3, 2, 1}) = 3 <-- swapping 1 with 2, increases inversion count by 1  
}  

### Input

Input consists of multiple cases, one case per line.Each case consists of a sequence of integers separated by comma. 

### Output

For each case, print exactly one line with the new inversion count or the original inversion count if it cannot be reduced.

### 样例输入

	3,1,2
	1,2,3,4,5

### 样例输出

	1
	0

### My Thoughts

本题确实没有想到什么比较好的方法。首先，只要一个序列的逆序数不为0，则必可以减小。其次，确定一个序列中的逆序数的快速方法有[(1)归并排序(2)树状数组](http://www.2cto.com/kf/201111/111542.html)两种方法。这样可以避免使用O(n*n)去计算逆序数。

另外一个点就是交换两个数字，只影响这两个数字作为区间中的逆序数对，没有想到什么好方法，只好计算如果交换这两个数对中间数字增加还是减小逆序数量。网友[echoth](http://www.cnblogs.com/echoht/p/3661425.html)提供了进一步优化的方法，只有当array[i]>array[j] (i<j)的情况下，交换i、j才能减小逆序数。

### My Code

``` c++

#include <iostream>
#include <cstring>
#include <string>
using namespace std;

int inversion;

void Merge(int* source, int* target, int startIndex, int midIndex, int endIndex){
	int i = startIndex;
	int j = midIndex+1;
	
	for(int k=startIndex; k<=endIndex; k++){
		if(source[i]<=source[j] && i<=midIndex && j<=endIndex){
			target[k] = source[i++];
		}
		else if(j<=endIndex){
			inversion+=(j-k);
			target[k] = source[j++];
		}
		else{
			target[k] = source[i++];
		}
	}
}

void MergeSort(int* source, int* target, int startIndex, int endIndex){
	if(startIndex==endIndex){
		target[startIndex] = source[startIndex];
	}
	else{
		int temp[51];
		int midIndex = (startIndex+endIndex)/2;
		MergeSort(source, temp, startIndex, midIndex);
		MergeSort(source, temp, midIndex+1, endIndex);
		Merge(temp, target, startIndex, midIndex, endIndex);
	}
}



int main(){

	int sourceArray[51];
	int targetArray[51];
	string str;

	while(cin>>str){
		
		// Handle the input.
		int len = str.length();
		int count = 0;
		int num = 0;
		for(int i=0; i<len; i++){
			if(str[i]>='0' && str[i]<='9'){
				num*=10;
				num+=(str[i]-'0');
			}
			else{
				sourceArray[count++] = num;
				num = 0;
			}
		}
		sourceArray[count++] = num;

		// Calculate the inversion.
		inversion = 0;
		MergeSort(sourceArray, targetArray, 0, count-1);

		// Calculate how many inversion pair can be reduced.
		int mostReduce = 0;
		int reduce = 0;
		for(int i=0; i<count; i++){
			for(int j=i+1; j<count; j++){
				if(sourceArray[i]>sourceArray[j]){
					reduce = 1;
					for(int k=i+1; k<j; k++){
						if(sourceArray[k]<sourceArray[i] && sourceArray[k]>sourceArray[j])
							reduce+=2;
						if(sourceArray[k]==sourceArray[i] || sourceArray[k]==sourceArray[j])
							reduce+=1;
					}	
				}
				if(reduce > mostReduce)
					mostReduce = reduce;
			}
		}
		cout<<inversion-mostReduce<<endl;
	}
	return 0;
}


```


----------------------------------------------------------------------------
----------------------------------------------------------------------------

### 题目4: Most Frequent Logs

时间限制:10000ms  
单点时限:1000ms  
内存限制:256MB  

### Description

In a running system, there are many logs produced within a short period of time, we'd like to know the count of the most frequent logs.

Logs are produced by a few non-empty format strings, the number of logs is N(1<=N<=20000), the maximum length of each log is 256.

Here we consider a log same with another when their edit distance (see note) is <= 5.

Also we have a) logs are all the same with each other produced by a certain format string b) format strings have edit distance  5 of each other.

Your program will be dealing with lots of logs, so please try to keep the time cost close to O(nl), where n is the number of logs, and l is the average log length.

Note edit distance is the minimum number of operations (insertdeletereplace a character) required to transform one string into the other, please refer to



http://en.wikipedia.org/wiki/Edit_distance for more details.

### Input

Multiple lines of non-empty strings.

### Output

The count of the most frequent logs.

### 样例输入

	Logging started for id:1
	Module ABC has completed its job
	Module XYZ has completed its job
	Logging started for id:10
	Module ? has completed its job

### 样例输出

	3

### My Thoughts

无想法...有想法再补充...

### My Code

无代码...

----------------------------------------------------------------------------

### Reference

1. [hihoCoder](http://hihocoder.com)
2. [微软的一道笔试编程试题. 开源中国社区.](http://www.oschina.net/code/snippet_928691_35005) 
3. [树状数组. 百度百科.](http://baike.baidu.com/link?url=nHj4hvkluZYY-8BO2z-JgAu1tLf0_PdtLVfTGmOzboDteP591uDkegrcuw90v6DNDP8HMm7R1BYKnEwo5-xWQa)
4. [逆序数及其求法. 红黑联盟.](http://www.2cto.com/kf/201111/111542.html)
5. [windmissiing. 求逆序数. CSND.](http://blog.csdn.net/mishifangxiangdefeng/article/details/7175642)
6. [echoht. 微软2014实习生及秋令营技术类职位在线测试-题目3: Reduce inversion count. 博客园.](http://www.cnblogs.com/echoht/p/3661425.html)
7. [潮风工作室. 微软2014实习生及秋令营技术类职位在线测试[题目与答案]. 博客园.](http://www.cnblogs.com/chaofeng/p/3661509.html)
