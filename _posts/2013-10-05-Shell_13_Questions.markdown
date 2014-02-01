---
layout: post
title:  "Shell 十三问"
date:   2013-10-05 12:00:00
tags:	tech
myTag:	shell

---

# Shell 十三问


本文是[CSDN shell 十三问](http://bbs.chinaunix.net/thread-218853-1-1.html)的学习笔记。一篇积存在收藏栏很久的文章，准备好好消化消化。

1. 为何叫做shell
-----------------

操作系统的其实最主要的部分就是它的"核心(kernel)"。我们没法直接操作这个核心，所以我们需要一个"外壳"程式来进行操作，也就是所谓的shell。简单来说，shell就是用来操作我们的操作系统的。

kernel和shell是不同的两套软件，而且都是可以被替换的：

+ 不同的操作系统使用不同的kernel
+ 同一kernel之上，也可以有不同的shell

不同的shell有着不同的功能，但是也大同小异，常见的shell主要分为量大主流：

sh:

	burne shell (sh)
	burne again shell (bash)

csh:

	c shell (csh)
	tc shell (tcsh)
	korn shell (ksh)

大部分Linux系统的预设shell为bash，其原因主要因为：

+ 开源软件
+ 功能强大

bash是gnu project最成功的产品之一，自推出以来深受Unix用户喜爱，且也逐渐成为不少组织的系统标准。


2. shell prompt(PS1)于Carriage Return(CR)的关系
-----------------------------------------------

如果你刚完成登录还没有任何按键之前，你所看到的游标所在位置的同一行的左边部分，我们称之为*提示符号*(prompt)。

提示符号的格式因不同系统版本而各有不同，在Linux上，只需六一最解决游标的一个可见的提示符号，通常是如下两者之一：

	$: 给一般使用者帐号使用
	#: 给root(管理员)帐号使用

实际上，shell prompt的意思很简单，就是告诉shell你现在可以输入命令了。CR的意思也很简单，就是告诉shell，你可以执行我的命令行了。(为什么我们在这里使用说CR字符而不是Enter呢？答案在后文中会提到)

不同的命令可接收的命令行格式或有不同，一般情况下，一个标准的命令行格式如下：

	command-name options argument

从技术细节来开，shell会根据IFS(Internal Field Seperator)将command line所输入的文字拆解为字段word。然后再针对特殊字符meta作先处理，最后再重新整行command line。

IFS是shell预设的栏位分隔符，可以由一个及多个如下按键组成：

+ 空白键(White Space)
+ 制表符(Tab)
+ 回车键(Enter)

系统可接收的命名名称(command-name)可以从以下突进获得：

+ 明确路径所指向的外部命令
+ 命令别名(alias)
+ 自定功能(function)
+ shell内建命令(built-in)
+ $PATH之下的外部命令


3. 试问echo知多少
-----------------

标准的command line包含三个部分：
	
	command-name option argument

ehco是一个简单直接的Linux命令，它将argumnet送出到标准输出(STDOUT)，通常是在屏幕输出。

为了更好理解，我们运行以下echo命令：

	$ echo

	$

你会发现只有一个空白行，然后又回到shell prompt上了。这是因为echo在预设上在显示完argument之后，还会送出一个换行符(new-line charactor)。如果你要取消这个换行符，可以利用echo的-n option：

	$ echo -n
	$

事实上，echo除了-n options之外，常用选项还有：

+ -e: 启用反斜线控制字符转换(参考下表)
+ -E: 关闭反斜线控制字符转换(预设如此)
+ -n: 取消行末之换行符(与-e选项下的\c字符同义)

echo命令支持的反斜线控制字符如下：

+ \a: ALERT / BELL (发出铃声)
+ \b: BACKSPACE，向左退格键
+ \c: 取消行末换行符
+ \E: ESCAPE，退出键
+ \f: FORFEED，换页字符
+ \n: NEWLINE，换行字符
+ \r: RETURN，回车键
+ \t: TAB，制表符
+ \v: VERTICAL TAB，垂直表格调位键
+ \n: ASCII 八进制编码(以x开头为十六进制)
+ \\\: 反斜线本身

例一：

	$ echo -e "a\tb\tc\nd\te\tf"
	a	b	c
	d	e	f

例二：
	
	$ echo -e "\141\011\142\011\143\012\144\011\145\011\146"
	a	b	c
	d	e	f

例二与例一结果一样，只是使用了ASCII八进制编码。

事实上，在日后shell操作以及shell script设计上，echo命令是最常被使用的命令之一。比方说，用echo检验变量值：

	$ A=B
	$ echo $A
	B
	$ echo $?
	0

(关于变量概念，后文会进行说明。)

4. " "(双引号)与' '(单引号)差在哪
---------------------------------

当你在command line输入的每一个文字，对于shell来说，是有类别之分的。command line的每一个charactor，分为如下两类：

+ literal: 也就似乎普通纯文字，对shell没有特殊功能。
+ mete: 对shell来说，具有特点功能的特殊保留字元。

除了IFS与CR之外，我们常用的meta还有：

+ =: 设定变量
+ $: 作变量或运算替换
+ >: 重导stdout
+ <: 重导stdin
+ |: 命令管线
+ &: 重导向file descriptor，或将命令置于背境执行
+ (): 将其内的命令置于nested subshell执行，或用于运算或替换
+ {}: 将其内的命令置于non-named function中执行，或用在变量替换的界定范围
+ ;: 在前一个命令结束时，而忽略其返回值，继续执行下一个命令
+ &&: 在前一个命令结束时，若返回值为true，继续执行下一个命令
+ ||: 在前一个命令结束时，若返回值为false，继续执行下一个命令
+ !: 执行history列表的命令

加入我们需要在command line中将这些保留字元的功能关闭的话，就需要quoting处理了。在bash中常用的quoting有如下三种方法：

+ hard quote: ''(单引号)，凡在hard quote中的所有meta均被关闭 
+ soft quote: ""(双引号)，在soft quote中的大部分meta都会被关闭，但某些则保留(如$)
+ escape: \(反斜线)，只有紧接在escape(跳脱字符)之后的单一meta才被关闭

下面的例子有助于我们对quoting的了解：

	$ A=B C					# 空白键未被关闭，作为IFS处理
	$ C: command not found
	$ echo $A

	$ A="B C"				# 空白键已被关闭，仅作为空白键处理
	$ echo $A
	B C

实际上，空白键无论在soft quote还是在hard quote中，均会被关闭。Enter键亦然：

	$ A='B
	> C
	> '
	$ echo "$A"
	B
	C

在上例中由于<enter>被置于hard quote当中，因此不再作为CR字符来处理。这里的<enter>单纯只是一个断行符号(new-line)而已，由于commnad line并没得到CR字符，因此在第二个shell prompt中，command line并不会结束，直到第三行，我们输入<enter>并不在hard quote里面，因此并没有被关闭，此时，command line碰到CR字符，于是结束，交给shell来处理。

上例的<enter>要是被置于soft quote中的话，CR也会同样被关闭：

	$ A="B
	> C
	> "
	$ echo $A
	B C

然而，由于echo $A时的变量没置于soft quote中，因此当变量替换完成后并作命令行重组时，<enter>会被解释为IFS，而不是解释为New Line字符。

同样的，用escape亦可关闭CR字符：

	$ A=B\
	> C\
	>
	$ echo $A
	BC


至于soft quote跟hard quote的不同，主要时对于某些meta的关闭与否，以$来作说明：

	$ A=B\ C
	$ echo "$A"
	B C
	$ echo '$A'
	$A

在第一个echo命令行中，$被至于soft quote中，将不被关闭，因此继续处理变量替换，因此echo将A的变量值输出导屏幕，也就得到"B C"的结果。在第二个echo命令中，$被置于hard quote中，则被关闭，因此$只是一个$符号，并不会用来作为变量替换处理，因此结果是$复合后面接一个A字母: $A。

以下是一个思考题：如果结果为什么不同：

	$ A=B\ C
	$ echo '"$A"'
	"$A"
	$ echo "'$A'"
	'B C'

(提示：单引号以及双引号，在quoting中均被关闭)


5. var=value? export前后差在哪
-------------------------------

在bash中，你可以用"="来设定或重新定义变量的内容：

	name=value

在设定变量的时候，得遵守如下规则：

+ 等号左右两边不能使用区隔符号(IFS)，也鹰笔名使用shell保留字(meta charactor)
+ 变量名称不能使用$符号
+ 变量名称的第一个字母不能是数字(number)
+ 变量名称长度不能超过256个字母
+ 变量名称以及变量值大小写敏感(case sensitive)

Shell之所以强大，其中的一个原因是它可以在命令行中对变量作替换(substitution)处理。在命令行中使用$符号加上变量名称将变量值替换出来，然后再重新组建命令行。

	$ A=ls
	$ B=la
	$ C=/tmp
	$ $A -$B $C

如果我们使用echo命令来查看特定变量的值，例如：

	$ echo $A -$B $C

那么上面的命令会在屏幕上得到如下结果：

	ls -la /tmp

此外，我们也可以利用命令行的变量替换能力来"扩充"(append)变量值：

	A=B:C:D
	A=$A:E

这样，第一行我们设定A的值为"B:C:D"，然后第二行再将它扩充为"B:C:D:E"。上面的扩充范围，我们使用区隔符号(:)来达到扩充目的，要是没有区隔符号，如下是游问题的：

	A=BCD
	A=$AE

因为第二次是将A的值继承$AE的提换结果，而非$A再加E。要解决这个问题，我们可以用更严谨的替换处理：

	A=BCD
	A=${A}E

上例中，我们使用{}将变量名称的范围给明确定义出来，如果一来，我们就可以将A的变量值从BCD给扩充为BCDE。

*export*

严格来说，我们在当前shell中所定义的变量，均属于"本地变量"(local variable), 只有经过export命令的输出处理，才能成为环境变量(environment variable)。

	$ A=B
	$ export A

或

	$ export A=B

经过export输出处理之后，变量A就能成为一个环境变量供其后的命令使用。

在使用export的时候，请不要忘记shell在命令行对变量的"替换"(substitution)处理，比如：

	$ A=B
	$ export $A

上面的命令中，$A会首先被替换成B，然后再作为export的参数。

要取消一个变量，在bash中可以使用unset命令来处理：

	unset A

与export一样，unset命令行也同样会作用为变量替换(这其实就是shell的功能之一)，因此：

	$ A=B
	$ B=C
	$ unset $A

事实上所取消的变量是B而不是A。

此外，变量一旦经过unset取消之后，其结果是将整个变量拿掉，不仅是取消其变量值。如下两行其实是不一样的：

	$ A=
	$ unset A

第一行只是将变量A设定为"空值"(null value)，但第二行则让便利A不再存在。
虽然用眼睛看，这两种变量状态在如下命令结果中都是一样的：

	$ A=
	$ echo $A

	$ unset A
	$ echo $A


我们必须识别null value与unset的本质区别，在一些进阶的变量处理上是很严格的。比如：

	$ str=				# 设为null
	$ var-${str=expr}	# 定义var
	$ echo $var

	$ echo $str

	$ unset str			# 取消
	$ var=${str=expr}	# 定义var
	$ echo $var
	expr
	$ echo $str
	expr

在这里我们需要知道var=${str=expr}在null与unset之下的不同。var=${str=expr}的意思是：

若str没有设置: 

	str=expr
	var=expr

若str已经设置:

	str不变
	var=$str


6. exec跟source差在哪
---------------------

首先提一个提问

***"cd /etc/aa/bb/cc可以执行，但是把这条命令写入shell时shell不执行，这是什么原因？(即运行脚本后没有移动到/ect/aa/bb/cc目录)"***

首先，我们所执行的任何程式，都是由父进程(parent process)所产生出来的一个子进程(child process)，子进程结束后，将返回父进程去。这一个现象在Linux系统中被称为fork。当子进程被产生出来的时候，将会从父进程那里获得一定的资源分配以及继承父进程的环境。所谓的`环境变量`就是那些会传给子进程的变量。所以简单而言，"遗传性"就是local variable与environment variable的决定性指标。然而，从遗传的角度来看，我们也不难发现环境变量的另一个重要特征：`环境变量只能从父进程到子进程单向继承`。换句话：在子进程的环境如何改变，均不会影响父进程的环境。

接下来，我们了解一下命令脚本(shell script)的概念。所谓的shell script就是讲平常在shell prompt后面输入的多行command line依次写入一个文件中去而已。其中再加上一些条件判断、互动界面、参数运用、函数调用等技巧，得以让script更好的执行。

再结合以上两个概念(process+script)，就不难理解如下这句话的意思：

***"正常来说，当我们执行一个shell script时，其实是先产生一个sub-shell的子进程，然后sub-shell再去产生命令行的子进程"***

但是我们更感兴趣的是如何解决这个问题。那么接下来，让我们了解一下source命令。

`所谓source就是让script在当前的shell内执行，而不是产生一个sub-shell来执行。`由于所有执行结果军事在当前shell内完成，若script的环境由所变化，当然也会改变当前环境了。比如我们是如此执行script的：

	./my.script

现在可以改成这样即可：

	source ./my.script

或：

	. ./my.script

*exec和source/fork有何不同*

简单来说，exec也是让script在同一个进程上执行，但是原有进程则被结束了。也就是原有进程会否终结，就是exec与source/fork的最大差异。

下面是两个简单的script，分别命名为1.sh以及2.sh

1.sh

	#!/bin/bash 
	A=B 
	echo "PID for 1.sh before exec/source/fork:$$"
	export A
	echo "1.sh: \$A is $A"
	case $1 in
	        exec)
	                echo "using exec..."
	                exec ./2.sh ;;
	        source)
	                echo "using source..."
	                source ./2.sh ;;
	        *)
	                echo "using fork by default..."
	                bash ./2.sh ;;
	esac
	echo "PID for 1.sh after exec/source/fork:$$"
	echo "1.sh: \$A is $A"

2.sh

	#!/bin/bash
	echo "PID for 2.sh: $$"
	echo "2.sh get \$A=$A from 1.sh"
	A=C
	export A
	echo "2.sh: \$A is $A"

然后再跑一下如下命令来观察结果：

	$ bash ./1.sh fork
	$ bash ./1.sh source
	$ bash ./1.sh exec

(P.S.在我的Mac上，除了exec跑不了之外其他都可以运行。)


7. ()与{}差在哪
---------------

我们在shell中，有时需要一定条件下一次执行多个命令，或时需要从一些命令执行优先次序，如数学式子2*(3+4)这样...这个时候，我们就可以引入"命令群组"(command group)的概念：将多个命令集中处理。

在shell command line中，虽然两者都可以将多个命令作群组化处理，但是在技术细节上是不一样的：

+ ()将command group置于sub-shell去执行，也称nested sub-shell。
+ {}则是在同一个shell内完成，也称non-named command group

要是在command group中扯上变量以及其他环境的修改，我们可以根据不同的需求来使用()或{}。

在这里我们补充一个概念`function`。所谓的function就是用一个名字去命名一个command group，然后再调用这个名字去执行command group。

在bash中，function的定义方式有两种:

方法一：

	function function_name {
		command1
		command2
		command3
		...
	}

方法二：

	function_name(){
		command1
		command2
		command3
		...
	}

使用哪一种方式无所谓，只是若碰到所定义的名称与现有的命令或别名(Alias)冲突的话，方法二或许会失败。


8. $(())与$()还有${}差在哪
--------------------------

在bash shell中，$()与``(反引号)都是用来做命令替换用(command substitution)的。完成引号里的命令行，然后将其结果替换出来，再重组命令行。

例如：

	$ echo Today is $(date "+%Y-%m-%d").

在操作上，用$()或``都无所谓。

在多层的复合替换中，\`\`需要额外的跳脱(\\\`)处理。而$()则比较直观，例如：

	command1 `command2 \`command3\``

相当于

	command1 $(command2 $(command3))

不过，$()并不是在每一种shell都能用，而``基本上可用于全部的unix shell中使用，若写成shell script，其移植性比较高。

接下来我们看看${}。它其实是用来作变量替换用的。一般情况下，$var与${var}并灭有什么不一样。但是用${}会比较精确的界定变量名称的范围。比如：

	$ A=B
	$ echo $AB

而实际上，我们只是想要输出$A的结果，再补一个B字母于后面，那么若使用${}就没问题：
	
	$ echo ${A}B
	BB

但其实$()的功能并不止界定变量名称，一下我们看一些S{}的一些特异功能：

假设我们定义一个变量为:
	
	file=/dir1/dir2/dir3/my.file.txt

我们可以用${}分别获得不同的值：

+ ${file#*/}:	拿掉第一个/以及左边的字串:	dir1/dir2/dir3/my.file.txt
+ ${file##*/}:	拿掉最后一个/以及左边的字串:	my.file.txt
+ ${file%/*}:	拿掉最后一个/以及右边的子串:	/dir1/dir2/dir3
+ ${dile%%/*}:	拿掉第一个/以及右边的子串:	(空值)

当然`/`能够换成`.`，但是其他字符我是了一下似乎不可以。

+ ${file:0:5}:	提取最左边的5个字节:	/dir1
+ ${file:5:5}:	提取第5个字节开始连续5个字节:	/dir2

我们同时也可以对变量里的字串作替换：

+ ${file/dir/path}:		将第一个dir替换为path:	/path1/dir2/dir3/my.file.txt
+ ${file//dir/path}:	将全部dir替换为path:	/path1/path2/path3/my.file.txt

此外，利用${}还可以针对不同的变量状态赋值(没设定、空值、非空值)：

+ ${file-my.file.txt}:	假设$file没有设定，则使用my.file.txt作为回传值。
+ ${file:my.file.txt}:	假设$file没有设定或为空值，则使用my.file.txt作回传值。
+ ${file+my.file.txt}:	假设$file设为空值或非空值，均使用my.file.txt作回传值。
+ ${file:+my.file.txt}:	假设$file为空值，则使用my.file.txt作回传值。
+ ${file=my.file.txt}:	假设$file没设定，则使用my.file.txt作回传值，同时将$file赋值为my.file.txt。
+ ${file:=my.file.txt}:	假设$file没设定或为空值，则使用my.file.txt作回传值，同时将$file赋值为my.file.txt。
+ ${file?my.file.txt}:	假设$file没设定，则将my.file.txt输出至STDERR。
+ ${file:?my.file.txt}:	假设$file没设定或为空值，则将my.file.txt输出至STDERR。

tips:

以上的理解在于，一定要分清unset, null以及non-null这三种赋值状态。一般而言，`:`于null有关，若不带`:`，null不受影响。

还有，${#var}可计算出变量的长度：${#file}可得到27。

接下来，介绍一下bash得数组(array)处理方法。一般而言，A="a b c def"这样的变量只是将$A替换为一个单一子串，但是改为A=(a b c def)，则是将$A定义为数组。

bash得数组替换方法可参考如下方法：

	${A[@]} 或 ${A[*]}

可得到`a b c def`(全部组数)。

同理:

	${A[0]}

可得a(第一个组数)，${A[1]}则为第二个组数...

	${#A[@]} 或 ${#A[*]}

可以得到4(全部组数数量)。

	${#A[0]}

可得到1(即第一个组数(a)的长度)，${#A[3]}可得到3(第四个组数(def)的长度)。

	A[3]=xyz

则是将第四个组数重新定义为xyz。

最后介绍$(())的用途：它是用来作整数运算的。在bash中，$(())的整数运输符大致有:

+ \+ \- \* /:	加 减 乘 除
+ %:	求余
+ & | ^ !:	AND OR XOR NOT 

在$(())中的变量名称中，可在其前面加$符号来替换，也可以不用，如：

	$ a=5; b=7; c=2
	$ echo $(( a + b * c ))
	19
	$ echo $(( $a + $b * $c ))
	19

事实上，单纯用(())也可以重新定义变量，或做testing：

+ a=5; ((a++))可将$a重新定义为6
+ a=5; ((a--))则为a=4
+ a=5; b=7; ((a < b))会得到0(true)的返回值

常用的(())的测试符号有：

+ <
+ >
+ <=
+ >=
+ ==
+ !=


9. $@与$*的区别
----------------

在shell script中，我们可用$0, $1, $2, $3...这样的变量分别提取命令行中的如下部分：

	script_name prarmeter1 parameter2 parameter3 ...

我们很容易知道`$0`代表`shell script`名称(路径)本身，而`$1`就是其后的第一个参数。需要留意的是IFS的作用。若IFS被quoting处理后，那么positional parameter也会改变。

***Positional parameter陷阱***

$10并不是替换第10个参数，而是替换第一个参数($1)然后再补一个0于其后。

要替换第十个参数的话，有两种方法：

方法一：使用`${}`，也就是用${10}即可。

方法二：使用`shift`。所谓的shift就是取消positional parameter中最左边的参数($0不受影响)。其预设值为1，也就是shift或shift 1都是取消$1, 而原本的$2则变成$1, $3变成$2...若shift 3则是取消前面三个参数。

***抓取参数数量***

通过`$#`可以抓出positional parameter的数量。因此，我们常再shell script里用如下方法测试script是否有读进参数:

	[ $# = 0 ]

加入为0，那么表示script没有参数，否则就是带有参数...

***$@与$\****

两者在soft quote中才有差异，否则都表示"全部参数"($0除外)。

例如在command line跑`my.sh p1 "p2 p3" p4`的话，都可得到`p1 p2 p3 p4`。但是如果至于soft quote的话：

+ "$@"得到"p1" "p2 p3" p4"
+ "S*"得到"p1 p2 p3 p4".

我们可以执行这样一个例子my.sh:

	#!/bin/bash

	my_fun(){
		echo "$#"
	}

	echo 'the nuber of parameter in "$@" is '$(my_fun "$@")
	echo 'the nuber of parameter in "$*" is '$(my_fun "$*")

再执行

	bash ./my.sh p1 "p2 p3" p4

得到结果

	the nuber of parameter in "$@" is 3
	the nuber of parameter in "$*" is 1


10. &&与||的区别
------------------

***Return Value***

我们再shell下跑的每一个command或者function，在结束的时候都会传回父进程一个值，称为`return value`。我们可以在shell command line中用`$?`这个变量得到最"新"的一个return value，也就是刚结束的传回值。

Return Value(RV)的取值为0-255之间，由程式(或script)的作者自行定义。

+ 若在script里，用exit RV来指定其值，若没指定，在结束时以最后一道命令的RV为值。+ 若在function里，则用return RV来代替exit RV即可。

Return Value的作用是用来判断进程的退出状态(exit status)，只有两种：

+ 0为true
+ 非0为false

例如：

	$ touch my.file
	$ ls my.file
	$ echo $?
	0
	$ ls no.file
	ls: no.file: No such file or directory
	$ echo $?
	1
	$ echo $?
	0

***test命令***

每一个command在结束时都会送回return value的。所以有一个命令是专门用来测试某一条件而送出return value以供真假判断的，他就是`test`命令了。下面作一些但要说明。

首先，test的表达式我们称为expression，其命令格式有两种：

	test expression

或

	[ expression ]

请务必注意[]之间的空白键!

其次，bash的test目前支持的测试对象只有三种：

+ string: 字符串(纯文字)
+ integer: 整数(0或正整数，不含负数或小数点)
+ file: 文件

这三种类型是有差别的，以`A=123`为变量为例：

+ [ "$A" = 123 ]: 是字符串的测试，测试$A是否为1、2、3这三个连续的文字。
+ [ "$A" -eq 123 ]: 是整数的测试，测试$A是否等于123这个数字。
+ [ -e "$A" ]: 是关于文件的测试，测试123这个文件是否存在。

同时，test也允许多重的复合测试：

+ expression1 -a expression2: 两者为真时为true，送出0；否则送出非0。
+ eppression1 -o expression2: 只需有一个为真时为true，送出0；两者都为假时送出0。

例如：

	[ -d "$file" -a -x "$file" ]

表示当$file是一个目录且同时拥有x权限时，test才为true。

***&&与||作用***

例子：

	$ A=123
	$ [ -n "$A" ] && echo "yes! it's true." || echo "no, it's NOT true."
	yes! it's true.
	$ unset A
	$ [ -n "$A" ] && echo "yes! it's true." || echo "no, it's NOT true."
	no, it's NOT ture.

其中[ -n string ]是测试string长度大于0则为true。`&&`与`||`相当于`and`和`or`。而且`||`的右边也只有当左边不成立时才会被执行。与C++里的规格相似。


11. >与<差在哪
----------------

***File Descriptor***

在shell程式中，最常用的`file descriptor(FD)`大概有三个，分别是：

+ 0: Standard Input (stdin)
+ 1: Standard Output (stdout)
+ 2: Standard Error Output (stderr)

在标准情况下，这些FD与如下设备(device)关联：

+ stdin(0): keyboard
+ stdout(1): monitor
+ stderr(2): monitor

***"<" 与 ">"***

我们可以用`<`来改变读入的数据通道(stdin)，使之从指定的文件读进。  
我们可以用`>`来改变输出的数据通道(stdout, stderr)，使之输出到特定文件。

那么`<<`又是什么意思呢？这是所谓的HERE Document，它可以让我们输入一段文字，知道读到<<后面指定的字串为止。例如：

	$ cat << FINISH
	> first line
	> second line
	> thrid line
	> FINISH
	first line
	second line
	thrid line

这样的话，cat会读取3行句子，而无需从keyboard读进数据且等^d结束输入。

严格来说，`<`符号之前是需要指定一个FD的(之间不能有空白)，但因为0是`<`的预设值，因此`<`与`0<`是一样的。  
同理，`1>`是改变stdout的数据输出通道；`2>`是改变stderr的数据输出通道。1是`>`的预设值。

当我们将stdout和stderr都指定到同一个文件，那么后来写的就会覆盖前面的。如果我们需要将stdout和stderr输出到同一个文件，可以`将stderr导进stdout或将stdout导进sterr`。例如：

	./out 1>file.both 2>&1

或

	./out 2>file.both >&2

***/dev/null***

在Linux系统里面，有个设备位于/dev/null。这个null在I/O Redirection中很有用。当程序运行，屏幕会同时送出stdout和stderr，如果我不像看到stdout或stderr(或两者)，那么我们可以将它们导向/dev/null。除了用`>/dev/null 2>&1`之外，我们还可以：

	$ ./out &>/dev/null

将`&>`换成`>&`可是可以的。

***覆盖文件***

如果我们想在已有文件末尾追加导入的内容，那么我们可以通过`>>`实现。

为了避免我们覆盖掉原有文件，我们可以进行设置：

	$ set -o noclobber
	$ echo "4" > file.out						//原来已经有file.out
	-bash: file: cannot overwrite existing file

需要取消这个限制的话可以将`-o`改成`+o`。

如果你想不取消这一限制，又临时能够覆盖目标文件，可以通过在`>`后面添加`|`实现。

	$ set -o noclobber 
	$ echo "6" >| file.out 
	$ cat file.out 
	6 

考虑这样一个例子，一个有内容的文件file，执行如下程序：

	$ cat < file > file
	$ cat < file

为什么会变成没有内容了？

原因是`在IO Redirection中，stdout和stderr的管道会先处理好再从stdin读进资料`。也就是上例中，`>file`先将file清空，然后再读进`<file`。

考虑以下两个例子：

	$ cat <> file			//将内容覆盖原内容并输出屏幕
	$ cat < file >> file	//不断在原内容后面添加原内容，死循环

***pipe line***

我们在command line上常看到的`|`符号就是`pipe line`了。pipe line的意思是`水管`，那么我们很容易理解，其实pipe line的I/O跟水管的I/O是一样的。

不管在command line上使用多少pipe line，前后两个command的I/O都是彼此连接的。

如果遇到stderr，它相当于水管漏水，不会接到下一个命令的stdin中，除非你使用2>导到file中去，否则它会现实在屏幕上。

在`cm1 | cm2 | cm3...`这个例子中，cm1的结果会作为cm2的输入，如此类推下去。

如果我们需要保存cm2的结果，我们可以通过：

	cm1 | cm2 > file ; cm3 < file

但这样子I/O次数就翻倍了。文件I/O是最常见的效能杀手，我们应尽量降低文件I/O的频率。

***tee命令***

在上例中，我们可以使用更好的方法：

	cm1 | cm2 | tee file | cm3

所谓的`tee`命令就是在不影响原来I/O的情况下，将stdout赋值一份到文件中去。如果需要追加而不是覆盖的话，可以使用参数`-a`。


12. 你要if还是case
---------------------

在前面几个问题中我们谈到了`&&`和`||`，我们当然能够有这两个符号完成命令选择这个功能，但是它们可读性比较差，所以我们可以引入`if...then...else...`:

	if comd1
	then
		comd2
	    comd3
	else
		comd4
		comd5
	fi

在if判断式中，else可以不要，但then是必须的。如果then后面不想跑任何命令可以使用`:`这个`null command`来代替。和python一样，也可以有`elif`。

	if comd1; then
	    comd2
	elif comd3; then
	    comd4
	else
	    comd5
	fi

当然我们还可以用`case`来简化我们的程序：

	case "$1" in
	  start)
	        start
	        ;;
	  stop)
	        stop
	        ;;
	  status)
	        rhstatus
	        ;;
	  restart|reload)
	        restart
	        ;;
	  condrestart)
	        [ -f /var/lock/subsys/syslog ] && restart || :
	        ;;
	  *)
	        echo $"Usage: $0 {start|stop|status|restart|condrestart}"
	        exit 1
	esac


13. for what? while与until差在哪
----------------------------------

bash shell中常用的循环有三种：

+ for
+ while
+ until

***for***

`for`循环是从一个清单中读入变量值，并依次在循环体中执行。

	for var in one two three four five
	do
		echo ---------------
		echo '$var is '$var
		echo
	done

上面例子会在循环体中运行五次，var的值依次是one two three four five。

倘若for循环没有使用`in`这个关键字来指定变量值清单的话，其值将从$@(或$*)中继承。

	for var; do
		...
	done

对于一些"累积变化"的项目，for也能处理：

	for((i=1; i<=10; i++))
	do
		echo "num is $i"
	done


***while***

上面的例子可以用while循环来改写：

	num=1
	while [ "$num" -le 10 ]; do
		echo "num is $num"
		num=$(($num + 1))
	done

***until***

until循环的语法如下：

	num=1
	until [ ! "$num" -le 10 ]; do
		echo "num is $num"
		num=$(($num + 1))
	done

until是在return value为false的时候才进入循环体。

***break, continue***

shell里也有break，但是与C++不同的是，shell中的break需要在后面跟一个数字。表示从里向外打断第n个循环。预设值为break 1，也就是跳出当前循环。同理continue预设值也为1，表示进入下一次循环。
