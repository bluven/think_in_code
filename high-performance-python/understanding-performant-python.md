# Understanding-Performant-PYthon

## Questions

* What are the elements of a computer’s architecture?* What are some common alternate computer architectures?* How does Python abstract the underlying computer architecture?* What are some of the hurdles to making performant Python code?* What are the different types of performance problems?

## The Fundamental Computer System

计算机系统有3个基本组件：

* 计算单元
* 记忆单元
* 两者之间的连接

每种单元都有自己独特的属性：

* 计算单元每秒可以执行多少次运算
* 记忆单元的容量和数据传送速度
* 连接单元的传送速度

一个计算机的CPU从内存和硬盘读写数据，它们之间通过总线连接，CPU内部还可能有L1,L2,L3,L4缓存，CPU与缓存间的连接总线叫做后端总线(backside bus)，计算机之间又可以用网线互连。

### 计算单元

计算单元的两个属性：

1. Instructions per cycle (IPC);
2. Cycles per second（Clock Speed)；

这两者之间有些竞争，有些计算单元IPC很高，但时钟速度很低，有些则相反。尽管搞时钟速度能极大影响计算性能，高IPC则能提高向量化(Vectorization)水平.

#### Vectorization
Vectorization is when a CPU is provided with multiple pieces of data at a time and is able to operate on all of them at once. This sort of CPU instruction is known as SIMD (Single Instruction, Multiple Data).

#### Hyperthreading
Hyperthreading presents a virtual second CPU to the host operating system (OS), and clever hardware logic tries to interleave two threads of instructions into the execution units on a single CPU. Typically this works well when the units of work across both threads use different types of execution unit—for example, one performs floating-point operations and the other performs integer operations.
   
#### Out-of-order
Out-of-order execution enables a compiler to spot that some parts of a linear program sequence do not depend on the results of a previous piece of work, and therefore that both pieces of work could potentially occur in any order or at the same time. As long as sequential results are presented at the right time, the program continues to execute correctly, even though pieces of work are computed out of their programmed order. This enables some instructions to execute when others might be blocked (e.g., waiting for a memory access), allowing greater overall utilization of the available resources.

#### Multicore Architectures 
Multicore architectures include multiple CPUs within the same unit, which increases the total capability without running into barriers in making each individual unit faster. While this increases the total number of operations that can be done per second, it introduces intricacies in fully utilizing both computing units at the same time.

Simply adding more cores to a CPU does not always speed up a program’s execution time. This is because of something known as Amdahl’s law. Simply stated, Amdahl’s law says that if a program designed to run on multiple cores has some routines that must run on one core, this will be the bottleneck for the final speedup that can be achieved by allocating more cores.

#### GIL In Python

The GIL makes sure that a Python process can only run one instruction at a time, regardless of the number of cores it is currently using. This means that even though some Python code has access to multiple cores at a time, only one core is running a Python instruction at any given time. 

While this may seem like quite a hurdle, especially if the current trend in computing is to have multiple computing units rather than having faster ones, this problem can be avoided by using other standard library tools, like multiprocessing, or technologies such as numexpr, Cython, or distributed models of computing.  

### Memory Units

记忆单元用来存储比特数据，不同的记忆单元之间的一个重大区别是其读写速度，不同的读写方式也会影响读写速度。顺序读写通常比随机读写好快得多。除了读写速度，还有延迟，即记忆单元查找数据的时间。机械硬盘的查找速度很慢，而内存快很多。记忆单元的读写速度通常跟容量大小成反比。

Because of this, many systems implement a tiered approach to memory: data starts in its full state in the hard drive, part of it moves to RAM, then a much smaller subset moves to the L1/L2 cache. This method of tiering enables programs to keep memory in different places depending on access speed requirements. When trying to optimize the memory patterns of a program, we are simply optimizing which data is placed where, how it is laid out (in order to increase the number of sequential reads), and how many times it is moved between the various locations. In addition, methods such as asynchronous I/O and preemptive caching provide ways to make sure that data is always where it needs to be without having to waste computing time—most of these processes can happen independently, while other calculations are being performed!

### Communications Layers

There are many different modes of communication, but they are all variants on a thing called a bus.

* Frontend bus is the connection between the RAM and the L1/L2 cache. It moves data that is ready to be transformed by the processor into the staging ground to get ready for calculation, and moves finished calculations out.

* External bus that acts as the main route from hardware devices (such as hard drives and networking cards) to the CPU and system memory. This bus is generally slower than the frontside bus.

* Backside bus. Being able to queue up data necessary for computation in large chunks on a slow bus (from RAM to cache) and then having it available at very fast speeds from the backside bus (from cache to CPU) enables the CPU to do more calculations without waiting such a long time.


It is clear, then, that the main property of a bus is its speed: how much data it can move in a given amount of time. This property is given by combining two quantities: how much data can be moved in one transfer (bus width) and how many transfers it can do per second (bus frequency).

## Idealized Computing Versus the Python Virtual Machine

Let’s analyze this code using our abstract model of computation and then draw com‐ parisons to what happens when Python runs this code. As with any abstraction, we will neglect many of the subtleties in both the idealized computer and the way that Python runs the code. However, this is generally a good exercise to perform before solving a problem: think about the general components of the algorithm and what would be the best way for the computing components to come together in order to find a solution. By understanding this ideal situation and having knowledge of what is actually hap‐ pening under the hood in Python, we can iteratively bring our Python code closer to the optimal code.

Code Example:

```python
import mathdef check_prime(number):    sqrt_number = math.sqrt(number) number_float = float(number)    for i in xrange(2, int(sqrt_number)+1):        if (number_float / i).is_integer(): return False    return True
    print "check_prime(10000000) = ", check_prime(10000000) # Falseprint "check_prime(10000019) = ", check_prime(10000019) # True
```

### Idealized computing

代码运行时，数据的值存储在内存中。为了计算sqrt_number和number_float,需要将值发送到CPU。理想情况下，只需要发送一次，然后这个值就存储在CPU的L1/L2缓存中，然后CPU计算并将计算结果返回给内存。这个方式之所以理想是因为减少了数值的传送次数（读写），因为从L1/L2读写数据更快。优化过程中要尽量减少数据的传送次数，尽量将数据放在计算单元附近。

循环内的代码，如果能同时将number_float和i的值同时传给cpu做检查，那比每次给cpu发送i的值要好。这是利用了CPU的向量化能力(vectorization), 为了实现这个，除了将float_number之外，最好还要将尽量多的i的值缓存在L1/L2。

Idealized Code:

```python
import mathdef check_prime(number):    sqrt_number = math.sqrt(number) 
    number_float = float(number)    numbers = range(2, int(sqrt_number)+1) 
    for i in xrange(0, len(numbers), 5):        # the following line is not valid Python code        result = (number_float / numbers[i:(i+5)]).is_integer() 
        if any(result):
            return False 
            
    return True
```

上面的代码是伪代码，它让cpu可以在同一时间执行5个检测，从而提高了效率。

