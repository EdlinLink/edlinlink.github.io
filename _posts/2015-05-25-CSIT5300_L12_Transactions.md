---
layout: post
title:  "[CSIT 5300] Lecture 12: Transactions"
date:   2015-05-25 12:00:00
tags:	tech
myTag:	database

---

# Lecture 12: Transactions

1. "Transaction"(事务)是数据库程序处理数据的一个单位。
2. 事务执行前需要数据库是具有“一致性的”consistent。
3. 事务执行期间，数据库可以是不具有一致性。
4. 当事务提交"committed"后，数据库必须具有一致性。

----------------------

为了保持数据完整性，数据库具有以下属性:

1. Atomicity. 事务中的操作要不全做，要不全不做。
2. Consistency. 事务执行要保持数据库的一致性。
3. Isolation. 就算同时执行多个事务，事务间不能互相影响。
4. Durability. 完成的事务需要被系统记录下来，就算系统发生异常也不影响。

----------------------

事务的状态

+ Active: 当事务正在执行。
+ Partially committed: 当最后一句statement执行完。
+ Failed: 当发现正常的事务无法执行。
+ Aborted: 当回滚回开始事务前的状态。Aborted后可以选择:
	+ restart: 重新执行事务
	+ kill: 杀死这个事务
+ Committed: 成功完成事务。
		
				[partially]
				[committed]-----[committed]
				/	|		
		[active]	|
				\	|
				[failed]--------[aborted]

----------------------

+ Serial Schedule: 表示A事务执行完再执行B事务。
+ Not Serial but Correct Schedule: 表示AB事务交替执行，但是结果正确。
+ Not Serial and Incorrect Schedule: 表示AB事务交替执行，但是结果不正确。
+ Serializability: 一个事务能够等价于"Serial Schedule"，被称为"serializable"。

----------------------

当两个事务的两个命令Ii, Ij分别执行下列命令可能会造成conflict。

1. Ii=read(Q), Ij=read(Q). No conflict.
2. Ii=read(Q), Ij=write(Q). Conflict.
3. Ii=write(Q), Ij=read(Q). Conflict.
4. Ii=write(Q), Ij=write(Q). Conflict.

----------------------

+ Conflict Equivalent: 一个schedule通过交换"非冲突指令"(non-confliting instruction)得到的新schedule与原schedule是conflit equicalent。
+ Conflict Serializable: 如果交换完后的schedule是serial schedule，那么我们称为conflict serializable。

----------------------

+ Recoverable schedule: 如果一个事务Tj读取了另一个先前Ti写入的对象，那么Ti的committed需要在执行Tj读取操作前面。(否则Tj committed后发现Ti需要回滚，那么Tj基于Ti的数据就不对了)

----------------------

+ Cascading Rollback: 一个事务failed导致其他还没committed的事务也需要回滚。
+ Cascadeless Schedules: 一个事务failed也不会导致其他事务回滚。

----------------------

"Precedence graph"是用来表示判断一个schedule是不是serializability的。如果事务Ti，Tj产生了冲突，Ti先执行，那么有一个有向边从Ti指向Tj。如果这个图产生了有向环，则这个schedule不是可序列化的。

-------------

### Exercise

Q1: 以下序列"R1(A) R2(A) W1(A) W2(B)"是不是Serializable ？

Ans: Yes, 它可以等价于序列"T2 T1"。前面两个Read不冲突可以交换位置，W2(B)是对B这个对象的可以提前不冲突。

-----------------------

Q2: "R2(A) R1(A) W1(A) W2(B) C2 C1" 是不是recoverable？是不是cascadeless ？

Ans: 是Recoverable以及cascadeless，因为没有事务读取另一个事务写入的对象。

	T1		T2
	-------------
			R2(A)
	R1(A)
	W1(A)
			W2(B)
			C2
	C1


# Lecture 12: Locking Protocols

如果"Precedence graph"产生了环，那么这个schedule是不能序列化的。为了避免这种情况，产生了"锁协议"。

---------------------

数据对象有2种锁的状态:

1. exclusive (X) mode. 数据对象可被“读取”与“写入”。X-lock.
2. shared (S) mode. 数据对象只可以被“读取”。S-lock.

---------------------

+ S-lock可以给多个事务同时获得，但是X-lock只能给单个事务获得。
+ 锁协议可能造成"死锁"(deadlock)的情况或者"饥饿"(starvation)的情况。

---------------------

Two-Phase Locking (2PL) Protocol:

+ Phase1 只能获得锁。Phase2 只能释放锁。
+ 2PL的schedule一定是conflict serializable。
+ conflict serializable的schedule不一定是2PL。

--------------------

+ Strict 2 PL协议: Cascading roll-back在2PL是有可能发生的。为了避免这一情况产生了一个严格一点的协议: 一个事务必须持有它所有的X-lock直到它commit/abort.
+ Rigorious 2PL协议: 一个事务必须持有它所有的锁直到它commit/abort.


# Lecture 12: Timestamp-based Protocols

Timestamps: 一个事务进入系统的时候会被分配一个时间戳，Ti进入系统得到时间戳TS(Ti)，然后Tj再进入系统得到时间戳TS(Tj)。TS(Ti) < TS(Tj)。

--------------------------

+ W-timestamp(Q): 用以记录任意一个事务最近的一次write(Q)操作。
+ R-timestamp(Q): 用以记录任意一个事务最近的一次read(Q)操作。

--------------------------

假设Ti想要执行read(Q):

1. 如果 TS(Ti) < W-timestamp(Q), rejected. Ti rolled back.

	因为它需要读取一个已经被其他事务改写的Q。Ti 重新执行后会得到最大的timestamp。

2. 如果 TS(Ti) >= W-timestamp(Q), executed. R-timestamp(Q) = TS(Ti).

假设Ti想要执行write(Q):

1. 如果 TS(Ti) < R-timestamp(Q), rejected. Ti rolled back.

	因为已经有其他事务读取了这个Q值。

2. 如果 TS(Ti) < W-timestamp(Q), rejected. Ti rolled back.

	因为已经有其他事务写入了这个Q值。

3. 其他情况，executed. W-timestamp(Q) = TS(Ti).

-------------------------

+ "时间戳协议"保证了"可序列化"并且不会有"死锁"发生。
+ 但schedule不一定是recoverable的。

-------------------------

+ 为了解决不一定能recoverable的问题，一个事务的所有write操作必须放到事务最后来做，当一个事务在执行写入操作时，其他事务不能执行。


