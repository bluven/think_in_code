# 操作系统学习——进程

## 概念

### 进程是什么

进程是执行中的程序，一个进程不仅仅是程序代码，即文本区，它也包含着用程序计数器(PC)与处理器寄存器中内容表示的当前活动。进程通常还会有进程栈(函数参数，返回地址，局部变量）与数据区（全局常量），还可能会有堆(进程运行时动态分配的内存)。

### 进程状态

一个进程在运行时会在多种状态中变化，通常会有如下几种状态：

* New. The process is being created.
* Running. Instructions are being executed.
* Waiting. The process is waiting for some event to occur (such as an I/O completion or reception of a signal).
* Ready. The process is waiting to be assigned to a processor.
* Terminated. The process has finished execution.

一个处理器同一时刻只能有一个运行中的进程，但可以有多个Waiting和Ready状态的进程。

![进程状态图](https://ss2.bdstatic.com/70cFvnSh_Q1YnxGkpoWK1HF6hhy/it/u=2859404340,3875457948&fm=27&gp=0.jpg)

### 进程控制块(Process Control Block)

进程在操作系统中是用进程控制块(PCB)表示的，一个PCB包含了许多与进程有关的信息：

* Process state. The state may be new, ready, running, waiting, halted, and so on.

* Program counter. The counter indicates the address of the next instruction to be executed for this process.

* CPU registers. The registers vary in number and type, depending on the computer architecture. They include accumulators, index registers, stack pointers, and general-purpose registers, plus any condition-code
information. Along with the program counter, this state information must be saved when an interrupt occurs, to allow the process to be continued correctly afterward.

* CPU-scheduling information. This information includes a process priority, pointers to scheduling queues, and any other scheduling parameters.

* Memory-management information. This information may include such items as the value of the base and limit registers and the page tables, or the segment tables, depending on the memory system used by the operating system 

* Accounting information. This information includes the amount of CPU and real time used, time limits, account numbers, job or process numbers, and so on.

* I/O status information. This information includes the list of I/O devices allocated to the process, a list of open files, and so on.

![进程间CPU切换](http://img.my.csdn.net/uploads/201303/18/1363591050_7915.jpg)

### 线程

目前讨论的进程都是一个执行了单线程的进程，这样的进程同一时间只能执行一个任务。现代操作系统已经延伸了进程的概念，允许一个进程拥有多个执行中的线程，进而可以同时执行多个任务，这个特性在多核系统上尤其有益。在支持多线程的系统中，PCB也包含了线程的相关信息，操作系统的其他组件也需要改变从而支持线程。

## 进程调度

- 进程调度系统的两个主要目标是：
    - 保持CPU忙碌，最大化CPU利用率
    - 为所有程序提供可接受的响应时间，尤其是交互性程序  
- 为了实现这些目标，调度器必须采用合适的策略来实现进程在CPU间的切换
- 这些目标可能冲突，尤其每次系统要切换进程时，也要占用CPU资源。

### 调度队列

- 所有的进程都会被放入一个作业队列(job queue)
- 处于Ready状态的进程会被放入就绪队列(ready queue)
- 等待一个设备的进程会被放入该设备的队列(device queue)
- 根据需要，系统也可能会有其他队列

![The ready queue and various I/O device queues](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_04_ProcessSwitch.jpg)

一个进程起初会被放入就绪队列，直到被分配到CPU执行。当进程在CPU上执行时，如下几种时间会发生：

- 进程发出I/O请求，然后被放入I/O队列
- 进程创建一个新的子进程，然后等待子进程的结束
- 中断发生，进程被强制性从CPU移走，放入就绪队列

![ Queueing-diagram representation of process scheduling](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_06_QueueingDiagram.jpg)

### 调度器

调度器以某种算法将系统中的进程在各种调度队列间相互迁移，直到进程结束。

1.长期调度器(long-term scheduler)

在批处理系统中，通常被提交的进程远远超出能够马上运行的进程数目，这些被保存在大容量存储设备上等待运行。

长期调度器又叫作业调度器(job scheduler)，执行频率低，调度间隔可能会有几分钟。它从缓存中选出等待运行的进程载入到内存中，使其进入Ready状态，从而控制内存中的进程数量。

像UNIX和Windows这种分时系统通常没有长期调度器。内核仅仅是简单地将提交的进程载入进内存，等待短期调度器去调度。

2.中期调度器(medium-term scheduler)

中期调度器的思想是将一些进程从内存中临时移出，从而减少调度器须要处理进程的数目。进程移出内存的机制
叫做换出(Swapping)。

3.短期调度器(short-term scheduler)

短期调度器(CPU调度器)运行频率高(100ms一次），所以必须快速地Ready队列中选取一个进程进行执行。

三种调度器能够在下图中找到相应的位置，它们在不同的时机对进程进行调度。

![Addition of a medium-term scheduling to the queueing diagram](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_07_QueuingDiagram2.jpg)

### 上下文切换(Context Switch)

进程切换需要保存当前进程的状态，并回复另一个进程的状态，这就是上下文切换。进程的状态保存在PCB中。进程切换是纯开销，所以速度必须块（几毫秒)。

### 进程操作

### 进程间通信

进程间通信有两种基本模型: 内存共享(shared memeory)与消息传递(message passing)。

内存共享是在协作的进程中共享同一块内存，进程通过读写这一块内存实现交流。内存共享交流速度快，但需要解决进程间冲突，分布式系统中实现共享内存很困难。创建共享内存时需要进程系统调用(system call)，一旦内存创建好，进程就可以正常读写了。

消息传递在传递短消息时非常有用，因为没有进程竞争。分布式系统中实现消息传递也更容易

最近的研究表明在多核系统中消息传递比共享内存的性能更好，因为共享内存面临缓存一致性的问题。随着核数的增加，消息传递可能会成为更受青睐的IPC机制。

![ Communications models: (a) Message passing. (b) Shared memory](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter3/3_12_CommunicationsModels.jpg)
