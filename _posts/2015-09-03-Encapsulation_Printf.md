---
layout: post
title:  "封装 printf()"
date:   2015-09-03 12:00:00
tags:	tech
myTag:	c++

---

# 封装 printf()

---------------------------------------------------

一般来说最简单的调试方法就是printf输出来看。但是一旦工程代码量大起来，而且上线阶段不能显示测试阶段的debug信息。一条一条将printf删除或注释掉是很麻烦的。于是很自然就会想到重载printf来并给一个flag去控制输不输出debug信息。

### 参考代码

	1.	#include <stdio.h>  
	2.	#include <stdarg.h> 
	3.
	4.	void debugPrintf(const char *cmd, ...)
	5.	{
	6.		printf("[Err]");
	7.		va_list args;
	8.		va_start(args, cmd);
	9.		vprintf(cmd, args);
	10.		va_end(args);
	11.		printf("\n");  
	12.	}  

这段代码是最简单的封装。这样封装后，debugPrint()的使用方法和printf()是一样的。

第1行表示包含standard input & output 标准输入输出库。  
第2行表示包含standard argument，C标准函数库，主要目的为让函数能够接收可变参数。  
第4行传入的第一个参数是字符串，也就是printf参数中，用引号括起来的部分。传入的第二个参数是"..."，表示它是一个可变参数，这个参数可能有，可能没有，可能长，可能短。  
第6行打印出"[Err]"字符串。  
第7行"va_list"是定义在"stdarg.h"中的宏:  

	#ifdef _M_ALPHA
		typedef struct {
			char *a0;	/* pointer to first homed integer argument */
			int offset;	/* byte offset of next parameter */
		} va_list;
	#else
		typedef char * va_list;
	#endif

\_M\_ALPHA是指DEC ALPHA（Alpha AXP）架构。所以一般情况下va_list所定义变量为字符指针。

<font color="#ADADAD"> *"DEC Alpha， 也称为Alpha AXP，是64位的 RISC 微处理器，最初由DEC公司制造，并被用于DEC自己的工作站和服务器中。作为VAX的后续被开发，支援VMS操作系统，如 Digital UNIX。不久之后开放源代码的操作系统也可以在其上运行，如Linux 和 BSD 。Microsoft 支持这款处理器，直到Windows NT 4.0 SP6 ，但是从Windows 2000 beta3 开始放弃了对Alpha的支持。" -- 百度百科* </font>

第8行"va_start"是也是"strarg.h"的宏定义:

	#define va_start(ap,v) ( ap = (va_list)&v + _INTSIZEOF(v) )	//第一个可选参数地址

	#define _INTSIZEOF(n) ( (sizeof(n) + sizeof(int) - 1) & ~(sizeof(int) - 1) )	//将n的长度补齐到4的倍数
	
第9行"vprintf"函数将格式化输出到sdtout中，函数原型是:

	int vprintf(char *format, va_list param);
	
第10行"va_end"的宏定义:
	
	#define va_end(ap) ( ap = (va_list)0 )	//将va_list可变参数列表指针置空

第11行输出回车换行符。

-------------------------------------------------------------------

### 封装成类

通过上面段代码，你已经可以按照你的意愿输出不同的内容来debug了，这个时候最好将debugPrintf写进一个类里面，设置一个flag变量来控制是否打印:

	#define	DEBUG	1
	#define	RELEASE	0
	
	class Utils{
	public:
		Utils(){	mode = DEBUG;	}

		void debugPrintf(const char *cmd, ...){
			if( mode == DEBUG){
				...
			}
		}

	private:
		int mode;
	};

-------------------------------------------------------------------------

### 输出颜色

我个人喜好是将printDebug信息分成两种，一种是输出成功信息printSuccDebug，另一种是输出失败信息printErrDebug。希望在终端的时候就颜色分明的显示出来，那么最好的方法就是根据Succ或者Err输出不同颜色。

<font color=#ADADAD>*终端的字符颜色是用转义序列控制的，是文本模式下的系统显示功能，和具体的语言无关。转义序列是以ESC开头，可以用"\033"完成相同的工作(ESC 的ASCII 码用十进制表示就是27，等于用八进制表示的33）*  -- emmoblin 《printf在终端输出时改变颜色》</font>

	\033["显示方式"; "前景色"; "背景色"m

	显示方式:
	0:	默认值
	1:	高亮
	4:	下划线
	5:	闪烁
	7:	反显
	22:	非粗体
	24:	非下划线
	25:	非闪烁
	27:	非反显

	前景色:
	30: 黑色
	31: 红色
	32: 绿色 
	33: 黄色
	34: 蓝色
	35: 洋红
	36: 青色
	37: 白色

	背景色:
	40: 黑色
	41: 红色
	42: 绿色 
	43: 黄色
	44: 蓝色
	45: 洋红
	46: 青色
	47: 白色

所以比较适合的方式是，Succ输出绿色，Err输出红色

	\033[0m			Default
	\033[1; 32; 40m Succ
	\033[1; 31; 40m Err

	printf( "\033[1; 31; 40m[Succ]");
	...
	printf( "\033[0m");

----------------------------------------------------------

### 最终参考代码

	#define	DEBUG	1
	#define	RELEASE	0
	
	class Utils{
	public:
		Utils(){	mode = DEBUG;	}

		void debugPrintf(const char *cmd, ...){
			if( mode == DEBUG){
				printf("[Debug] ");
				va_list args;
				va_start(args, cmd);
				vprintf(cmd, args);
				va_end(args);
				printf("\n");  
			}
		}

		void debugSuccPrint(const char *cmd, ...){
			if( mode == DEBUG){
				printf( "\033[1; 31; 40m[Succ] ");
				va_list args;
				va_start(args, cmd);
				vprintf(cmd, args);
				va_end(args);
				printf( "\033[0m\n");
			}
		}

		void debugErrPrint(const char *cmd, ...){
			if( mode == DEBUG){
				printf( "\033[1; 30; 40m[Err] ");
				va_list args;
				va_start(args, cmd);
				vprintf(cmd, args);
				va_end(args);
				printf( "\033[0m\n");
			}
		}

	private:
		int mode;

	};

### 题外话

我还在网上看到一种封装printf很机智的方式，写成宏:

	#define DBG_PRINT (printf("%s:%u %s:%s:\t", __FILE__, __LINE__, __DATE__, __TIME__), printf)

这种方法虽然不能按照不同的需求输出参数，方式也非常的赞。

-----------------------------------------------------------

### 参考资料

1. [stdio.h. 百度百科](http://baike.baidu.com/view/538727.htm)
2. [stdarg.h. 百度百科](http://baike.baidu.com/link?url=0vGqch3u3eSJDqzUos3FbjQ0Lmp5XUiaqWoBzYYAhJmTjnI752Sz0Xt6J7NiB9IiS1_IK5zo9ZMikyGaPBdQ_q)
3. [va_list. 百度百科](http://baike.baidu.com/link?url=i3hYq0JSfksxRN_id9UCk4_AK9f2rlG717XUw2FdIArskH5SSkIR3DsgD6eYkXPBRcSVgz1lpEMK6CO6G94zS_)
4. [alpha. 百度百科](http://baike.baidu.com/link?url=rUb2q0AA2ZnwS16cR61MDm3TLndXXJPIHGSfTRR8fNP5WSToxcLtwAlolnXdD5zoS-Td82DV1XrVHzsb03R7S6f-QA7ZZVxnuqwlvIInsRq)
5. [#define _INTSIZEOF(n) ( (sizeof(n) + sizeof(int) - 1) & ~(sizeof(int) - 1) )=>中的 & ~什么意思. 百度知道](http://zhidao.baidu.com/link?url=S62W6mB5EbHsFCJEfoNLc9bH-rDcBYg9H3hvN9bYhgueO9AFvz4vf9VoOtB92_FDatc6W_E2AcPKcyNTNtRkF_)
6. [vprintf. 百度百科](http://baike.baidu.com/link?url=d-EONMiJZVGO_yox0xBth8TmNZ6xsA_9mOJwgP_-iMgYMuiSTd6sBceoRsa62HVypWYg-g5QW0Zq60FvtI5o8a)
7. [fprintf 的封装（vsprintf,va_start(), va_arg(), va_end()可变参数列表）. 海王](http://www.cnblogs.com/leaven/archive/2010/06/29/1767374.html)
8. [printf在终端输出时改变颜色. emmoblin](http://blog.chinaunix.net/uid-20778443-id-94545.html)
9. [封装自己的printf函数. Maddock](http://www.cnblogs.com/adong7639/p/4186779.html)

