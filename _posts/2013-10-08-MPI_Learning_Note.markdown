---
layout: post
title:  "MPI Learning Note (update: Oct 12)"
date:   2013-10-08 00:00:00
myTag:	mpi

---

# MPI Learning Note


因为学习工作需要，需要学习MPI，用它来做并行化的处理，所以就顺带写一下学习笔记。我使用的Mac原来已经安装好MPI了。你也可以在终端上输入`mpi`再按`Tab`键，看看是否已经安装与MPI相关的东西(例如mipcc，mpirun...)本文就直接从安装后开始学习。

本文主要参考清华大学`都志辉`老师的《高性能计算之并行编程技术——MPI并行程序设计》一书。

## 第一个MPI程序

我们新建一个C语言文件a.c:

	#include "mpi.h"
	#include <stdio.h>
	#include <math.h>

	int main(int argc, char*argv[]){

		int myid, numprocs;
		int namelen;
		char processor_name[MPI_MAX_PROCESSOR_NAME];

		MPI_Init(&argc, &argv);
		MPI_Comm_rank(MPI_COMM_WORLD, &myid);
		MPI_Comm_size(MPI_COMM_WORLD, &numprocs);
		MPI_Get_processor_name(processor_name, &namelen);

		fprintf(stderr, "Hello World! Process %d of %d on %s\n", myid, numprocs, processor_name);
		
		MPI_Finalize();

		return 0;
	}

输入如下命令。编译，执行：

	mpicc a.c -o out
	mpirun -np 4 .out

输出结果：

	Hello World! Process 0 of 4 on Edlin-Mac.local
	Hello World! Process 2 of 4 on Edlin-Mac.local
	Hello World! Process 1 of 4 on Edlin-Mac.local
	Hello World! Process 3 of 4 on Edlin-Mac.local

`MPI`其实不是一个软件，而是一个库。它能够实现多台计算机并行处理一个程序。当然前提是这个程序是能够并行化的。那我们首先谈谈上面的第一个MPI程序。

C+MPI的并行程序主要分成四部分：

1. 要有MPI相对于C的头文件`mpi.h`  
2. 定义程序中一些与MPI有关的变量：
	+ myid: 某一个进程的标识
	+ numprocs: 所有参加计算的进程的个数
	+ MPI\_MAX\_PROCESSOR\_NAME: 允许机器名字的最大长度
	+ processor\_name: 机器名字
	+ namelen: 实际机器名字的长度
3. MPI程序的开始和结束必须是`MPI_Init`和`MPI_Finalize`，处理初始化和结束工作。  
4. MPI程序主体，包括各种MPI过程调用语句和C语句。
	+ MPI\_Comm\_rank: 得到当前正在运行的进程的标识号，存放在myid中
	+ MPI\_Comm\_size: 得到所有参加运行的进程的个数，存放在numprocs中
	+ MPI\_Get\_processor\_name: 得到本进程运行的机器的名字，存放在processor\_name中，字符串类型，该长度存放在namelen中。

我们执行的命令使得该程序产生4个进程，他们同时执行，将运行进程的编号、进程总数和机器名字输出(因为我都是只在我自己电脑运行，所以都显示`Edlin-Mac.local`)。它们没有先后次序之分，只要最后输出了这4条语句，那么程序就是正确的。


## 六个接口构成的MPI子集

在MPI-1中，共有128个调用接口(在MPI-2中有287个)，但是其实MPI所有通信功能可以用它的6个基本调用来实现，也就是只要掌握这6个调用，就可以实现所有消息传递并行程序的功能。下面是对这6个调用的简介。


### 1. MPI初始化 MPI_INIT

`MPI_INIT`是MPI程序的第一个调用，它完成MPI程序所有的初始化工作，所有MPI程序的第一条可执行语句都是这条语句。

对于C语言调用，需要给出参数argc和argv(可参考第一个MPI程序这个例子)，`注意这里给出的是argc和argv`。

	MPI_INIT()
	int MPI_Init(int *argc, char ***argv)
	MPI_INIT(IERROR)
		INTEGER IERROR

### 2. MPI结束 MPI_FINALIZE

`MPI_FINALIZE`是MPI程序的最后一个调用，它结束MPI程序的运行，是MPI程序最后一条可执行语句，否则程序的运行结果是不可预知的。

	MPI_FINALIZE()
	int MPI_Finalize(void)
	MPI_FINALIZE(IERROR)
		INTEGER IERROR

### 3. 当前进程标识 MPI\_COMM\_RANK

	MPI_COMM_RANK(comm, rank)
		IN	comm	该进程所在通信域
		OUT	rank	调用进程在comm中的标识号
	int MPI_Comm_rank(MPI_Comm comm, int *rank)
	MPI_COMM_RANK(COMM, RANK, IERROR)
		INTEGER COMM, RANK, IERROR

这一调用返回调用进程在所给定的通信域中的进程标识号，有了这一标识号，不同的进程就可以将自身和其它的进程区别开来，实现各进程的并行和协作。

### 4. 通信域包含的进程数 MPI_COMM_SIZE

	MPI_COMM_SIZE(comm, size)
		IN	comm	通信域
		OUT	size	通信域comm内包含的进程数(整数)
	int MPI_Comm_size(MPI_Comm comm, int *size)
	MPI_COMM_SIZE(COMM, SIZE, IERROR)
		INTEGER COMM, SIZE, IERROR

这一调用返回给定的通信域所包含的进程的个数，不同的进程通过这一调用得知在给定的通信域中一共有多少各进程在并行执行。

### 5. 消息发送 MPI_SEND

	MPI_SEND(buf, count, datatype, dest, tag, comm)
		IN	buf		发送缓冲区的起始地址(可选类型)
		IN	count	将发送的数据的个数(非负整数)
		IN	datatype 发送数据的数据类型(句柄)
		IN	dest	目的进程标识号(整数)
		IN	tag		消息标志(整数)
		IN	comm	通信域(句柄)
	int MPI_Send(void* buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm)
	MPI_SEND(BUF, COUNT, DATATYPE, DEST, TAG, COMM, IERROR)
		<type> BUF(*)
		INEGER COUNT, DATATYPE, DEST, DEST, TAG, COMM, IERROR

`MPI_SEND`将发送缓冲区中的count个datatype类型的数据发送到目的进程。目的进程在通信域中的标识号是dest，本次发送的消息标志是tag，使用这一标志，就可以把本次发送的消息和本进程向同一目的进程发送的其它消息区分开来。

`MPI_SEND`操作指向的发送缓冲区是由count个类型为datatype的连续数据空间组成，起始地址为buf。但不是以字节计数，而是以数据类型为单位指定消息的长度。

其中`datatype`数据类型可以是MPI的预定义类型，也可以是用户自定义的类型。

### 6. 消息接收 MPI_RECV

`MPI_RECV`从指定的进程source接收消息，并且该消息的数据类型和消息标识和本接收进程的指定的datatype和tag一致，接收到的消息所包含的数据元素个数最多不超过count个。

接收缓冲区由count个类型为datatype的连续元素空间组成，其实地址为buf。接收到消息的长度必须小于等于接收缓冲区的长度，否则可能发生溢出错误。

	MPI_RECV(buf, count, datatype, source, tag, comm, status)
		OUT buf		接收缓冲区的起始地址(可选数据类型)
		IN	count	最多可接收的数据个数(整型)
		IN	datatype 接收数据的数据类型(句柄)
		IN	source	接收数据的来源即发送数据的进程的进程标识号(整型)
		IN	tag		消息标识，与相应的发送操作的标识相匹配相同(整型)
		IN	comm	本进程和发送进程所在的通信域(句柄)
		IN	status	返回状态(状态类型)
	int MPI_Recv(void* buf, int count, MPI_Datatype datatype, int source, int tag, MPI_Comm comm, MPI_Status *status)
	MPI_RECV(BUF, COUNT, DATATYPE, SOURCE, TAG, COMM, STATUS, IERROR)
		<type> BUF(*)
		INTEGER COUNT, DATATYPE, SOURCE, TAG, COMM, STATUS(MPI_STATUS_SIZE), IERROR

### 返回状态status

返回状态变量`status`用途很广，是MPI定义的一个数据类型，使用之前需要用户为它分配空间。在C实现中，状态变量是由至少三个域组成的结构类型。这三个域分别是：`MPI_SOURCE`, `MPI_TAG`, `MPI_ERROR`。它还可以包含其它附加域。这样通过对`status.MPI_SOURCE`, `status.MPI_TAG`和`status.MPI_ERROR`的引用，就可以得到返回状态中所包含的发送数据进程的标识，tag标识和本操作返回的错误代码。

此外对`status`变量执行`MPI_GET_COUNT`调用可以得到接收到的消息的长度信息。

### 一个简单的发送接收例子

	#include "mpi.h"
	#include <stdio.h>
	#include <string.h>
	main(int argc, char* argv[]){

		char message[20];
		int myrank;
		MPI_Status status;
		MPI_Init(&argc, &argv);
		MPI_Comm_rank(MPI_COMM_WORLD, &myrank);

		if(myrank==0){
			strcpy(message, "Hello, process 1");
			MPI_Send(message, strlen(message), MPI_CHAR, 1, 99, MPI_COMM_WORLD);
		}
		else if(myrank==1){
			MPI_Recv(message, 20, MPI_CHAR, 0, 99, MPI_COMM_WORLD, &status);
			printf("received: %s", message);
		}
		MPI_Finalize();
	}

上面这个例子中`MPI_COMM_WORLD`是包含本进程(进程0)和接收消息的进程(进程1)的通信域。发送方和接收方都必须在同一个通信域中。由通信域来统一协调和控制消息发送接收。


### MPI消息传递过程

MPI的消息传递过程可以分为三个阶段：

1. 消息装配：将发送数据从发送缓冲区取出，加上消息信封等形成一个完整的消息。  
2. 消息传递：将装配好的消息从发送端传递到接受端。  
3. 消息拆卸：从接收到的消息中取出数据送入接收缓冲区。

在这些阶段中都需要类型匹配。

MPI消息包括`信封`和`数据`两个部分。信封指出发送或接收消息的对象及相关信息，而数据本身是本消息将要传递的内容。

以MPI_SEND和MPI_RECV为例：

	MPI_SEND(buf, count, datatype, dest, tag, comm)
	消息数据：buf, count, datatype
	消息信封：dest, tag, comm

	MPI_RECV(buf, count, datatype, source, tag, comm, status)
	消息数据：buf, count, datatype
	消息信封：source, tag, comm

tag能赋值为`MPI_ANY_TAG`，那么它能匹配任意的tag值；source能赋值为`MPI_ANY_SOURCE`，那么它能够接收来自任意进程的信息。MPI_ANY_TAG和MPI_ANY_SOURCE能同时或单独使用。MPI也允许进程给自己发送消息，但要注意死锁的产生。


## 简单的MPI程序示例

### 获取机器的名字和MPI版本号

在实际编程的时候，我们可能需要保存一些中间结果或最终结果在自己创建的文件中，对于不同机器上运行的进行，常希望输出的文件包含该机器名字，这样仅仅考标识rank是不够的，MPI有一个专门的调用。使各个进程在运行的时候能动态获得该进程运行的机器名字。

	MPI_GET_PROCESSOR_NAME(name, resultlen)
		OUT	name		当前进程所运行机器的名字
		OUT	resultlen	返回名字的长度(以可打印字符的形式)
	int MPI_Get_precessor_name(char *name, int *resultlen)
	MPI_GET_PRECESSOR_NAME(NAME, RESULTLEN, IERROR)
		CHARACTER *(*)NAME
		INTEGER RESULTLEN, IERROR

MPI_GET_PROCESSOR_NAME调用返回进程所在机器的名字。

	MPI_GET_VERSION(version, subversion)
		OUT	version
		OUT	subversion
	int MPI_Get_verion(int *version, int *subversion)
	MPI_GET_VERSION(VERSION, SUBVERSION, IERROR)
		INTEGER VERSION, SUBVERSION, IERROR


### 数据接力传递

<center> ![数据接力传递示例图](../picture/MPI_Learning_Note-1.png)</center>

	#include <stdio.h>
	#include "mpi.h"
	int main(int argc, char* argv[]){

		int rank, value, size;
		MPI_Status status;
		MPI_Init(&argc, &argv); 
		MPI_Comm_rank(MPI_COMM_WORLD, &rank);
		MPI_Comm_size(MPI_COMM_WORLD, &size);

		do{
			if(rank==0){
				fprintf(stderr, "\nPlease give new value=");
				scanf("%d", &value);
				fprintf(stderr, "%d	read	<-	(%d)\n", rank, value);
				if(size>1){
					MPI_Send(&value, 1, MPI_INT, rank+1, 0, MPI_COMM_WORLD );
					fprintf(stderr, "%d	send	(%d)->	%d\n", rank, value, rank+1);
				}
			}
			else{
				MPI_Recv(&value, 1, MPI_INT, rank-1, 0, MPI_COMM_WORLD, &status);
				fprintf(stderr, "%d	receive	(%d)<-	%d\n", rank, value, rank-1);
				if(rank < size-1){
					MPI_Send(&value, 1, MPI_INT, rank+1, 0, MPI_COMM_WORLD);
					fprintf(stderr, "%d	send	(%d)->	%d\n", rank, value, rank+1);
				}
			}
			MPI_Barrier(MPI_COMM_WORLD);
		
		} while(value >= 0);

		MPI_Finalize();
	}

用7个进程运行该程序，分别输入76和-3，执行结果如下:

	Please give new value=73
	0	read	<-	(73)
	0	send	(73)->	1
	1	receive	(73)<-	0
	1	send	(73)->	2
	2	receive	(73)<-	1
	2	send	(73)->	3
	3	receive	(73)<-	2
	3	send	(73)->	4
	4	receive	(73)<-	3
	4	send	(73)->	5
	5	receive	(73)<-	4
	5	send	(73)->	6
	6	receive	(73)<-	5

	Please give new value=-3
	0	read	<-	(-3)
	0	send	(-3)->	1
	1	receive	(-3)<-	0
	1	send	(-3)->	2
	2	receive	(-3)<-	1
	2	send	(-3)->	3
	3	receive	(-3)<-	2
	3	send	(-3)->	4
	4	receive	(-3)<-	3
	4	send	(-3)->	5
	5	receive	(-3)<-	4
	5	send	(-3)->	6
	6	receive	(-3)<-	5

需要说明一下上面这个例子的`MPI_Barrier`函数表示`阻止调用直到communicator中所有进程完成调用`。也即是`进程0`会等到`进程6`调用完之后再继续执行。(因为本例子共有7个进程)

### 任意进程间问候

<center> ![任意进程间问候示例图](../picture/MPI_Learning_Note-2.png)</center>
		
	#include "mpi.h"
	#include <stdio.h>
	#include <stdlib.h>

	void Hello(void);

	int main(int argc, char *argv[]){
		
		int me, option, namelen, size;
		char processor_name[MPI_MAX_PROCESSOR_NAME];
		MPI_Init(&argc, &argv);
		MPI_Comm_rank(MPI_COMM_WORLD, &me);
		MPI_Comm_size(MPI_COMM_WORLD, &size);

		if(size < 2){
			fprintf(stderr, "systest requires at least 2 processes");
			MPI_Abort(MPI_COMM_WORLD, 1);
		}

		MPI_Get_processor_name(processor_name, &namelen);
		fprintf(stderr, "Process %d is alive on %s\n", me, processor_name);
		MPI_Barrier(MPI_COMM_WORLD);

		Hello();

		MPI_Finalize();
	}


	void Hello(void){
		int nproc, me;
		int type = 1;
		int buffer[2], node;
		MPI_Status status;
		MPI_Comm_rank(MPI_COMM_WORLD, &me);
		MPI_Comm_size(MPI_COMM_WORLD, &nproc);

		if(me==0){
			printf("\nHello test from all to all\n");
			fflush(stdout);
		}

		for(node=0; node<nproc; node++){
			if(node!=me){
				buffer[0] = me;
				buffer[1] = node;

				MPI_Send(buffer, 2, MPI_INT, node, type, MPI_COMM_WORLD);
				MPI_Recv(buffer, 2, MPI_INT, node, type, MPI_COMM_WORLD, &status);

				if((buffer[0]!=node) ){
					(void)fprintf(stderr, "Hello: %d!=%d or %d!=%d\n", buffer[0], node, buffer[1], me);
					printf("Mismatch on hello process ids; node = %d\n", node);
				}
				printf("Hello from %d to %d\n", me, node);
				fflush(stdout);
			}
		}
	}

用3个进程执行上面这个例子，得到如下输出：

	Process 0 is alive on Edlin-Mac.local
	Process 1 is alive on Edlin-Mac.local
	Process 2 is alive on Edlin-Mac.local

	Hello test from all to all
	Hello from 0 to 1
	Hello from 0 to 2
	Hello from 1 to 0
	Hello from 2 to 0
	Hello from 2 to 1
	Hello from 1 to 2


### 任意源和任意标识的使用

<center> ![任意源和任意标识的使用示例图](../picture/MPI_Learning_Note-3.png)</center>
	
	#include "mpi.h"
	#include <stdio.h>

	int main(int argc, char* argv[]){

		int rank, size, i, buf[1];
		MPI_Status status;
		MPI_Init(&argc, &argv);
		MPI_Comm_rank(MPI_COMM_WORLD, &rank);
		MPI_Comm_size(MPI_COMM_WORLD, &size);

		if(rank==0){
			for(i=0; i<100*(size-1); i++){
				MPI_Recv(buf, 1, MPI_INT, MPI_ANY_SOURCE, MPI_ANY_TAG, MPI_COMM_WORLD, &status);
				printf("Msg=%d from %d with tag %d\n", buf[0], status.MPI_SOURCE, status.MPI_TAG);
			}
		}
		else{
			for(i=0; i<100; i++){
				buf[0]=rank+1;
			MPI_Send(buf, 1, MPI_INT, 0, i, MPI_COMM_WORLD);
			}
		}
		MPI_Finalize();
	}


***如果你已经看到这里了，表示你已经懂得MPI的基本使用方法了。下面我们将进入学习MPI的下一个问题。***



## MPI并行程序的两种基本模式

1. 对等模式: 每个进程都是同等重要，一个进程从另外几个进程得到返回数据。  
2. 主从模式: 有一个主进程，主进程分配任务给各个其它进程，其它进程返回计算结果给主进程。例如矩阵相乘。

## 不同的通信模式

MPI提供了四种通信机制，我们之前所有的例子都是基于`标准通信模式(standard mode)`，其它的三种分别是`缓存通信模式(buffered-mode)`，`同步通信模式(synchronous-mode)`和`就绪通信模式(ready-mode)`。

MPI通信模式：  
+ 标准通信模式	MPI_SEND	MPI_RECV  
+ 缓存通信模式	MPI_BSEND  
+ 同步通信模式	MPI_SSEND  
+ 就绪通信模式	MPI_RSEND  

### 标准通信模式

MPI的标准通信模式中，是否对所发送的数据进行缓存是由MPI自身来决定，而不是由程序员决定的。例如：

1. 进程0发送消息(不依赖接收进程) --> 缓存消息 --> 缓存完毕 --> 发送完成  
2. 进程0发送消息(依赖接收进程) --> 不缓存消息 --> 直接发送 --> [进程1接收消息 --> 开始接收 --> 全部接收] --> 发送完成  

由于缓存数据是会延长数据通信时间，而且缓冲区也并不是总可以得到的，这样MPI也可以不缓存将要发出的数据，这样只有当相应的接收调用被执行之后，并且发送数据完全到达接收缓冲区后，发送操作才算完成。对于非阻塞通信，发送操作虽然没有完成，但是发送调用可以正确返回，程序可以接下来执行其它操作。

### 缓存通信模式

在缓存通信模式下，用户可以直接对通信缓冲区进行申请、使用和释放，因为，缓存模式下对通信缓冲区的合理和正确使用是由程序设计人员自己保证的。

	MPI_BSEND(buf, count, datatype, dest, tag, comm)
		IN	buf			发送缓冲区的起始地址(可选数据类型)	
		IN	count		发送数据的个数(整型)
		IN	datatype	发送数据的数据类型(句柄)
		IN	dest		目标进程标识号(整数)
		IN	tag			消息标志(整型)
		IN	comm		通信域(句柄)
	int MPI_Bsend(void* buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm)
	MPI_BSEND(BUF, COUNT, DATATYPE, DEST, TAG, COMM, IERROR)
		<type> BUF(*)
		INTEGER COUNT, DATATYPE, DEST, TAG, COMM, IERROR

采用缓存通信模式，消息发送是否能够进行以及正确返回，完全依赖于是否有足够的通信缓冲区可用。当缓存发送并返回后，并不意味着该缓冲区可以自由使用，只是当缓冲区的消息发送出去后，才可以释放该缓冲区。

用户可以首先申请缓冲区，然后将它提交给MPI作为发送缓存，用于支持发送进程的缓存通信模式。这样，当缓存通信方式发生，MPI就可以使用蛇蝎缓冲区对消息进行缓存。当不使用这些缓冲区时，可以将它们释放。

	MPI_BUFFER_ATTACH(buffer, size)
		IN	buffer		初始缓存地址(可选数据类型)
		IN	size		按字节计数的缓存跨度(整型)
	int MPI_Buffer_attach(void* buffer, int size)
	MPI_BUFFER_ATTACH(BUFFER, SIZE, IERROR)
		<type> BUFFER(*)
		INTEGER SIZE, IERROR

`MPI_BUFFER_ATTACH`将大小为size的缓冲区提交给MPI，这样该缓冲区就可以作为发送时的缓存来使用。

	MPI_BUFFER_DETACH(buffer, size)
		OUT	buffer		缓冲区初始地址(可选数据类型)
		OUT	size		以字节为单位的缓冲区大小(整型)
	int MPI_Buffer_detach(void** buffer, int* size)
	MPI_BUFFER_DETACH(BUFFER, SIZE, IERROR)
		<type> BUFFER(*)
		INTEGER SIZE, IERROR

`MPI_BUFFER_DETACH`将提交的大小为size的缓冲区buffer收回。该调用是阻塞调用，它一直等到该缓存的消息发送完毕后才返回。这一调用返回后用户可以重新使用该缓冲区或将这一缓冲区释放。

### 同步通信模式

	MPI_SSEND(buf, datatype, dest, tag, comm)
		IN	buf			发送缓冲区的起始地址(可选数据类型)	
		IN	count		发送数据的个数(整型)
		IN	datatype	发送数据的数据类型(句柄)
		IN	dest		目标进程标识号(整数)
		IN	tag			消息标志(整型)
		IN	comm		通信域(句柄)
	int MPI_Ssend(void* buf, int count, MPI_Datatype, int dest, int tag, MPI_Comm comm)
	MPI_SSEND(BUF, COUNT, DATATYPE, DEST, TAG, COMM, IERROR)
		<type> BUF(*)
		INTEGER COUNT, DATATYPE, DEST, TAG, COMM, IERROR

同步通信模式的开始不依赖于接收进程相应的接艘操作是否已经启动，但是同步发送取必须等到相应的接收进程开始孩子后才能正确返回。因此，同步发送返回后，意味着发送缓冲区中的数据已经完全被系统缓冲区缓存，并且已经开始发送。这样当同步发送返回后，发送缓冲区可以被释放或重新使用。

### 就绪通信模式

	MPI_SSEND(buf, datatype, dest, tag, comm)
		IN	buf			发送缓冲区的起始地址(可选数据类型)	
		IN	count		发送数据的个数(整型)
		IN	datatype	发送数据的数据类型(句柄)
		IN	dest		目标进程标识号(整数)
		IN	tag			消息标志(整型)
		IN	comm		通信域(句柄)
	int MPI_Ssend(void* buf, int count, MPI_Datatype, int dest, int tag, MPI_Comm comm)
	MPI_SSEND(BUF, COUNT, DATATYPE, DEST, TAG, COMM, IERROR)
		<type> BUF(*)
		INTEGER COUNT, DATATYPE, DEST, TAG, COMM, IERROR

在就绪通信模式中，只有当接收进程的操作已经启动时，才可以在发送进程启动发送操作，否则当发送操作启动而相应的接收还没有启动时，发送操作将出错。对于非阻塞发送操作的正确返回，并不意味着发送已经完成，但对于阻塞发送的正确返回，则发送缓冲区可以重复使用。


标准通信模式之外的通信模式是MPI提供给程序员的附加的并行程序通信手段，它要求程序员要有更好的更精确的理解，在此基础上，根据不同的需要，使用不同的通信模式，可以达到优化和提高效率的目的。

至此，你已经了解了MPI的基本使用方法，如果想要继续深入学习，可以继续参考清华大学`都志辉`老师的《高性能计算之并行编程技术——MPI并行程序设计》一书。: )
