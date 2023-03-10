# 我的Goroutine认知

## 进程 vs 线程 vs 协程

### [进程](https://en.wikipedia.org/wiki/Process_(computing))

* 创建成本最高，因为包含一个程序运行的所有信息，指令，静态数据，初始的内存，系统分配的一些资源描述符(文件)，安全属性，都会比较大；销毁成本也很高，要释放之前分配的所有资源。

* 进程间通信成本高，通过管道，各种通信协议通信

* 上下文切换成本高，寄存器内容，内存地址信息，可能涉及到[缓存信息的丢失](https://stackoverflow.com/questions/5440128/thread-context-switch-vs-process-context-switch)

​      [Context Switch](https://en.wikipedia.org/wiki/Context_switch)

### 线程

* (内核线程)通过系统调用创建，跟其他线程共享大多数进程资源，创建时会分配2M左右的堆栈内存，该内存不会增长，程序递归调用时可能出现栈溢出错误；创建成本可以用线程池解决；

* 上下文切换成本比进程低，一些必须的寄存器，不包含地址空间转换

* 可以在进程内通过内存共享的方式通信

* 根据系统的实现不同，有时候进程进行阻塞操作依然会占用CPU，浪费算力。有些操作系统会将阻塞的线程调走，对于IO密集型应用来说，频繁的上下文切换会浪费太多算力。针对IO密集型应用可以利用异步IO编程，select， epoll等方式，进而催化nodejs，go等异步编程语言的诞生；

* 使用层面可以进行主动让度

  https://lass.cs.umass.edu/~shenoy/courses/fall16/lectures/Lec06.pdf
  [Change default Linux thread stack size](https://trac.osgeo.org/mapguide/ticket/1016)
  [Default stack size for pthreads](https://unix.stackexchange.com/questions/127602/default-stack-size-for-pthreads)

### 协程

* 非操作系统调度，而是用户层面的调度

* 不利用系统调用，直接在用户空间创建，创建时分配2kb的栈内存，后面可以按需扩展，扩展时会将原来的数据复制进来；

* userspace调度，不能基于时间片分配；上下文切换成本低，只涉及几个寄存器；天生为IO密集型场景设计，遇到阻塞性操作，调度器会让其他协程运行；调度时机？

* 配合Go自带的channel，通信成本更低；

* 内存局部性

## 内核态与用户态

https://medium.com/geekculture/linux-cpu-context-switch-deep-dive-764bfdae4f01
	
### 待补充

1. 协程数据结构
2. 系统调用
3. 协程栈内存特性
4. 补充线程，进程，协程的调度机制及影响
5. 内核态，用户态的区别

## Go协程设计

### 调度模型

#### GPM

* G: 协程，有三种状态：running, runnable, not-runnable(due to blocking IO or system call)
* M: 线程，负责执行协程，必须有P才能执行。遇到一些阻塞性操作(系统调用)，可能会被剥夺P，调度器会另建线程执行其他的协程。
* P: 处理器，可以被视为运行在线程上的本地调度器。P数量是固定的，可以被配置，默认是CPU核数的双倍。M必须获取P后才能处理协程，直到因为阻塞被剥夺P。提出P模型的好处时，负责调度的资源固定，无论是Work Stealing还是GC，只需要处理这些P上的资源就够了。



#### 协程

三种状态:

* running
* runnable
* not-runnable

导致协程阻塞的原因:

* 阻塞性IO
* Channel。channel有waitq和recvq，阻塞的协程会在这些队列里等待
* Mutexes
* Timer
* 系统调用。
  * 阻塞性系统调用，会导致执行协程的内核线程阻塞，无法执行其他协程。调度器会创建或寻找其他线程来执行协程，被阻塞的线程继续执行原协程，执行完后改协程会被放入runnable队列
  * 非阻塞性系统调用，只会导致协程被阻塞，被netpoller接管，线程继而执行其他协程。这类系统调用通常都是IO操作，netpoller会在资源准备好时将对应的协程放到runnable队列。

三个协程队列:

* Global
* Local
* Network Poller

获取协程的先后顺序:

* Local
* Global
* Network Poller
* Work Stealing

![Go Scheduler Model](https://b3019442.smushcdn.com/3019442/wp-content/uploads/2022/07/go-golang-scheduler-example.png?lossy=1&strip=1&webp=1)



#### 抢占式调度

Go 1.1版本前的调度器不支持抢占式调度，程序只能依靠协程主动让出CPU才能触发调度，Go 语言的调度器在 1.2 版本中引入基于协作的抢占式调度解决下面的问题：

- 某些 Goroutine 可以长时间占用线程，造成其它 Goroutine 的饥饿；
- 垃圾回收需要暂停整个程序（Stop-the-world，STW），最长可能需要几分钟的时间[6](https://draveness.me/golang/docs/part3-runtime/ch06-concurrency/golang-goroutine/#fn:6)，导致整个程序无法工作；

1.2 版本的抢占式调度虽然能够缓解这个问题，但是它实现的抢占式调度是基于协作的，在之后很长的一段时间里 Go 语言的调度器都有一些无法被抢占的边缘情况，例如：for 循环或者垃圾回收长时间占用线程，这些问题中的一部分直到 1.14 才被基于信号的抢占式调度解决。

##### 基于协作的抢占式调度

* 编译器会在调用函数前插入 [`runtime.morestack`](https://draveness.me/golang/tree/runtime.morestack)
* Go 语言运行时会在垃圾回收暂停程序、系统监控发现 Goroutine 运行超过 10ms 时发出抢占请求 `StackPreempt`；
* 当发生函数调用时，可能会执行编译器插入的 [`runtime.morestack`](https://draveness.me/golang/tree/runtime.morestack)，它调用的 [`runtime.newstack`](https://draveness.me/golang/tree/runtime.newstack) 会检查 Goroutine 的 `stackguard0` 字段是否为 `StackPreempt`；
* 如果 `stackguard0` 是 `StackPreempt`，就会触发抢占让出当前线程；

##### 基于信号的抢占式调度

### netpoller

netpoller有自己的线程，接受来自执行IO操作的协程事件，利用操作系统提供的异步IO接口观测IO操作的状态。如果一个go协程发起IO操作，待相关资源没到位时，netpoller会接管该协程，让执行协程的线程执行其他协程，当netpoller发现某个协程需要的资源就绪后，会通知协程，然后协程尝试IO操作。

## 资料

* [Illustrated Tales of Go Runtime Scheduler](https://medium.com/@ankur_anand/illustrated-tales-of-go-runtime-scheduler-74809ef6d19b)
* [netpoller](https://morsmachine.dk/netpoller)
* [go scheduler](https://morsmachine.dk/go-scheduler)
* 

