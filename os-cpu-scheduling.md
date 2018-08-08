# CPU Scheduling


---

## 6.1 Basic Concepts

- Almost all programs have some alternating cycle of CPU number crunching and waiting for I/O of some kind. ( Even a simple fetch from memory takes a long time relative to CPU speeds. )
- In a simple system running a single process, the time spent waiting for I/O is wasted, and those CPU cycles are lost forever.
- A scheduling system allows one process to use the CPU while another is waiting for I/O, thereby making full use of otherwise lost CPU cycles.
- The challenge is to make the overall system as "efficient" and "fair" as possible, subject to varying and often dynamic conditions, and where "efficient" and "fair" are somewhat subjective terms, often subject to shifting priority policies.
    
### 6.1.1 CPU-I/O Burst Cycle

- Almost all processes alternate between two states in a continuing cycle, as shown in Figure 6.1 below :
- A CPU burst of performing calculations, and
- An I/O burst, waiting for data transfer in or out of the system.
![Figure 6.1 - Alternating sequence of CPU and I/O bursts.  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_01_CPU_BurstCycle.jpg)

- CPU bursts vary from process to process, and from program to program, but an extensive study shows frequency patterns similar to that shown in Figure 6.2:

![Figure 6.2 - Histogram of CPU-burst durations](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_02_CPU_Histogram.jpg)

### 6.1.2 CPU Scheduler

- Whenever the CPU becomes idle, it is the job of the CPU Scheduler ( a.k.a. the short-term scheduler ) to select another process from the ready queue to run next.
- The storage structure for the ready queue and the algorithm used to select the next process are not necessarily a FIFO queue. There are several alternatives to choose from, as well as numerous adjustable parameters for each algorithm, which is the basic subject of this entire chapter.

### 6.1.3. Preemptive Scheduling

- CPU scheduling decisions take place under one of four conditions:
    - When a process switches from the running state to the waiting state, such as for an I/O request or invocation of the wait( ) system call.
    - When a process switches from the running state to the ready state, for example in response to an interrupt.
    - When a process switches from the waiting state to the ready state, say at completion of I/O or a return from wait().
    - When a process terminates.
- For conditions 1 and 4 there is no choice - A new process must be selected.
- For conditions 2 and 3 there is a choice - To either continue running the current process, or select a different one.
- If scheduling takes place only under conditions 1 and 4, the system is said to be **non-preemptive**, or cooperative. Under these conditions, once a process starts running it keeps running, until it either voluntarily blocks or until it finishes. Otherwise the system is said to be **preemptive**.
- Windows used non-preemptive scheduling up to Windows 3.x, and started using pre-emptive scheduling with Win95. Macs used non-preemptive prior to OSX, and pre-emptive since then. **Note that pre-emptive scheduling is only possible on hardware that supports a timer interrupt.**
- Note that pre-emptive scheduling can cause problems when two processes share data, because one process may get interrupted in the middle of updating shared data structures. 
- Preemption can also be a problem if the kernel is busy implementing a system call ( e.g. updating critical kernel data structures ) when the preemption occurs. Certain operating systems, including most versions of UNIX, deal with this problem by waiting either for a system call to complete or for an I/O block to take place before doing a context switch. Unfortunately this solution is problematic for real-time systems, as real-time response can no longer be guaranteed. 
- Some critical sections of code protect themselves from con currency problems by disabling interrupts before entering the critical section and re-enabling interrupts on exiting the section. Needless to say, this should only be done in rare situations, and only on very short pieces of code that will finish quickly, ( usually just a few machine instructions. )

### 6.1.4 Dispatcher

- The dispatcher is the module that gives control of the CPU to the process selected by the scheduler. This function involves:
    - Switching context.
    - Switching to user mode.
    - Jumping to the proper location in the newly loaded program.
- The dispatcher needs to be as fast as possible, as it is run on every context switch. The time consumed by the dispatcher is known as dispatch latency

### 6.2 Scheduling Criteria

- There are several different criteria to consider when trying to select the "best" scheduling algorithm for a particular situation and environment, including:
    - **CPU utilization** - Ideally the CPU would be busy 100% of the time, so as to waste 0 CPU cycles. On a real system CPU usage should range from 40% ( lightly loaded ) to 90% ( heavily loaded. )
Throughput - Number of processes completed per unit time. May range from 10 / second to 1 / hour depending on the specific processes.
    - **Turnaround time** - Time required for a particular process to complete, from submission time to completion. ( Wall clock time. )
    - **Waiting time** - How much time processes spend in the ready queue waiting their turn to get on the CPU.
        - ( Load average - The average number of processes sitting in the ready queue waiting their turn to get into the CPU. Reported in 1-minute, 5-minute, and 15-minute averages by "uptime" and "who". )
    - **Response time** - The time taken in an interactive program from the issuance of a command to the commence of a response to that command.

In general one wants to optimize the average value of a criteria ( Maximize CPU utilization and throughput, and minimize all the others. ) However some times one wants to do something different, such as to minimize the maximum response time.

Sometimes it is most desirable to minimize the variance of a criteria than the actual value. I.e. users are more accepting of a consistent predictable system than an inconsistent one, even if it is a little bit slower.

## 6.3 Scheduling Algorithms

The following subsections will explain several common scheduling strategies, looking at only a single CPU burst each for a small number of processes. Obviously real systems have to deal with a lot more simultaneous processes executing their CPU-I/O burst cycles.

### 6.3.1 First-Come First-Serve Scheduling, FCFS

- FCFS is very simple - Just a FIFO queue, like customers waiting in line at the bank or the post office or at a copying machine.
- Unfortunately, however, FCFS can yield some very long average wait times, particularly if the first process to get there takes a long time. For example, consider the following three processes:
    Process	   | Burst Time
    ----       | ---
    P1         | 12
    P2         | 3
    P3         | 3
     
- In the first Gantt chart below, process P1 arrives first. The average waiting time for the three processes is ( 0 + 24 + 27 ) / 3 = 17.0 ms.
- In the second Gantt chart below, the same three processes have an average wait time of ( 0 + 3 + 6 ) / 3 = 3.0 ms. The total run time for the three bursts is the same, but in the second case two of the three finish much quicker, and the other process is only delayed by a short amount.
![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_FCFS_Chart1.jpg)
![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_FCFS_Chart2.jpg)

- FCFS can also block the system in a busy dynamic system in another way, known as the convoy effect. When one CPU intensive process blocks the CPU, a number of I/O intensive processes can get backed up behind it, leaving the I/O devices idle. When the CPU hog finally relinquishes the CPU, then the I/O processes pass through the CPU quickly, leaving the CPU idle while everyone queues up for I/O, and then the cycle repeats itself when the CPU intensive process gets back to the ready queue.

### 6.3.2 Shortest-Job-First Scheduling, SJF

- The idea behind the SJF algorithm is to pick the quickest fastest little job that needs to be done, get it out of the way first, and then pick the next smallest fastest job to do next.
- ( Technically this algorithm picks a process based on the next shortest CPU burst, not the overall process time. )
- For example, the Gantt chart below is based upon the following CPU burst times, ( and the assumption that all jobs arrive at the same time. )
    
    Process	   | Burst Time
    ----       | ---
    P1         | 6
    P2         | 8
    P3         | 7
    P4         | 3
![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_SJF_Chart.jpg)

- In the case above the average wait time is ( 0 + 3 + 9 + 16 ) / 4 = 7.0 ms, ( as opposed to 10.25 ms for FCFS for the same processes. )
- SJF can be proven to be the fastest scheduling algorithm, but it suffers from one important problem: How do you know how long the next CPU burst is going to be?
    - For long-term batch jobs this can be done based upon the limits that users set for their jobs when they submit them, which encourages them to set low limits, but risks their having to re-submit the job if they set the limit too low. However that does not work for short-term CPU scheduling on an interactive system.
    - Another option would be to statistically measure the run time characteristics of jobs, particularly if the same tasks are run repeatedly and predictably. But once again that really isn't a viable option for short term CPU scheduling in the real world.
    - A more practical approach is to predict the length of the next burst, based on some historical measurement of recent burst times for this process. One simple, fast, and relatively accurate method is the exponential average, which can be defined as follows. 
    ``` 
    estimate[ i + 1 ] = alpha * burst[ i ] + ( 1.0 - alpha ) * estimate[ i ]
    ```
- In this scheme the previous estimate contains the history of all previous times, and alpha serves as a weighting factor for the relative importance of recent data versus past history. If alpha is 1.0, then past history is ignored, and we assume the next burst will be the same length as the last burst. If alpha is 0.0, then all measured burst times are ignored, and we just assume a constant burst time. Most commonly alpha is set at 0.5, as illustrated in Figure 5.3:
![Figure 6.3 - Prediction of the length of the next CPU burst.  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_03_BurstPrediction.jpg)

- SJF can be either preemptive or non-preemptive. Preemption occurs when a new process arrives in the ready queue that has a predicted burst time shorter than the time remaining in the process whose burst is currently on the CPU. Preemptive SJF is sometimes referred to as **shortest remaining time first scheduling.**
-  For example, the following Gantt chart is based upon the following data:
    
    Process	   | Arrival Time | Burst Time
    ----       | ---          |
    P1         | 0            | 8
    P2         | 1            | 4 
    P3         | 2            | 9
    P4         | 3            | 5
![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_PreemptiveSJF_Chart.jpg)
- The average wait time in this case is ( ( 5 - 3 ) + ( 10 - 1 ) + ( 17 - 2 ) ) / 4 = 26 / 4 = 6.5 ms. ( As opposed to 7.75 ms for non-preemptive SJF or 8.75 for FCFS. )

### 6.3.3 Priority Scheduling

- Priority scheduling is a more general case of SJF, in which each job is assigned a priority and the job with the highest priority gets scheduled first. ( SJF uses the inverse of the next expected burst time as its priority - The smaller the expected burst, the higher the priority. )
- Note that in practice, priorities are implemented using integers within a fixed range, but there is no agreed-upon convention as to whether "high" priorities use large numbers or small numbers. This book uses low number for high priorities, with 0 being the highest possible priority.
- For example, the following Gantt chart is based upon these process burst times and priorities, and yields an average waiting time of 8.2 ms:

    Process	   | Burst Time   | Priority
    ----       | ---          | ---
    P1         | 10           | 3
    P2         | 1            | 1 
    P3         | 2            | 4
    P4         | 1            | 5
    P5         | 5            | 2
    ![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_PriorityChart.jpg)
    
- Priorities can be assigned either internally or externally. Internal priorities are assigned by the OS using criteria such as average burst time, ratio of CPU to I/O activity, system resource use, and other factors available to the kernel. External priorities are assigned by users, based on the importance of the job, fees paid, politics, etc.
- Priority scheduling can be either preemptive or non-preemptive.
- Priority scheduling can suffer from a major problem known as **indefinite blocking**, or **starvation**, in which a low-priority task can wait forever because there are always some other jobs around that have higher priority.
    - If this problem is allowed to occur, then processes will either run eventually when the system load lightens ( at say 2:00 a.m. ), or will eventually get lost when the system is shut down or crashes. ( There are rumors of jobs that have been stuck for years. )
    - One common solution to this problem is **aging**, in which priorities of jobs increase the longer they wait. Under this scheme a low-priority job will eventually get its priority raised high enough that it gets run.
           
### 6.3.4 Round Robin Scheduling

- Round robin scheduling is similar to FCFS scheduling, except that CPU bursts are assigned with limits called time quantum.
    - When a process is given the CPU, a timer is set for whatever value has been set for a time quantum.
If the process finishes its burst before the time quantum timer expires, then it is swapped out of the CPU just like the normal FCFS algorithm.
    - If the timer goes off first, then the process is swapped out of the CPU and moved to the back end of the ready queue.
- The ready queue is maintained as a circular queue, so when all processes have had a turn, then the scheduler gives the first process another turn, and so on.
- RR scheduling can give the effect of all processors sharing the CPU equally, although the average wait time can be longer than with other scheduling algorithms. In the following example the average wait time is 5.66 ms. 
    
    Process	   | Burst Time   | Priority
    ----       | ---          | ---
    P1         | 10           | 24
    P2         | 1            | 3 
    P3         | 2            | 3

![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_RoundRobinChart.jpg)

- The performance of RR is sensitive to the time quantum selected. If the quantum is large enough, then RR reduces to the FCFS algorithm; If it is very small, then each process gets 1/nth of the processor time and share the CPU equally.
- BUT, a real system invokes overhead for every context switch, and the smaller the time quantum the more context switches there are. ( See Figure 6.4 below. ) Most modern systems use time quantum between 10 and 100 milliseconds, and context switch times on the order of 10 microseconds, so the overhead is small relative to the time quantum.
 
![Figure 6.4 - The way in which a smaller time quantum increases context switches.  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_04_quantums.jpg)

- Turn around time also varies with quantum time, in a non-apparent manner. Consider, for example the processes shown in Figure 6.5:
![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_05_TurnaroundTime.jpg)
- In general, turnaround time is minimized if most processes finish their next cpu burst within one time quantum. For example, with three processes of 10 ms bursts each, the average turnaround time for 1 ms quantum is 29, and for 10 ms quantum it reduces to 20. However, if it is made too large, then RR just degenerates to FCFS. A rule of thumb is that 80% of CPU bursts should be smaller than the time quantum.

### 6.3.5 Multilevel Queue Scheduling

- When processes can be readily categorized, then multiple separate queues can be established, each implementing whatever scheduling algorithm is most appropriate for that type of job, and/or with different parametric adjustments.
- Scheduling must also be done between queues, that is scheduling one queue to get time relative to other queues. Two common options are strict priority ( no job in a lower priority queue runs until all higher priority queues are empty ) and round-robin ( each queue gets a time slice in turn, possibly of different sizes. )
- Note that under this algorithm jobs cannot switch from queue to queue - Once they are assigned a queue, that is their queue until they finish.

![Figure 6.6 - Multilevel queue scheduling  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_06_MultilevelQueueScheduling.jpg) 

### 6.3.6 Multilevel Feedback-Queue Scheduling

- Multilevel feedback queue scheduling is similar to the ordinary multilevel queue scheduling described above, except jobs may be moved from one queue to another for a variety of reasons:
        - If the characteristics of a job change between CPU-intensive and I/O intensive, then it may be appropriate to switch a job from one queue to another.
        - Aging can also be incorporated, so that a job that has waited for a long time can get bumped up into a higher priority queue for a while.
- Multilevel feedback queue scheduling is the most flexible, because it can be tuned for any situation. But it is also the most complex to implement because of all the adjustable parameters. Some of the parameters which define one of these systems include:
    - The number of queues.
    - The scheduling algorithm for each queue.
    - The methods used to upgrade or demote processes from one queue to another. ( Which may be different. )
    - The method used to determine which queue a process enters initially.
    
![Figure 6.7 - Multilevel feedback queues.  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_07_MultilevelFeedbackQueues.jpg)

## 6.4 Thread Scheduling

- On operating systems that support them, it is kernel-level threads—not processes—that are being scheduled by the operating system.
- User threads are mapped to kernel threads by the thread library - The OS ( and in particular the scheduler ) is unaware of them.

### 6.4.1 Contention Scope

- **Contention scope** refers to the scope in which threads compete for the use of physical CPUs.
- On systems implementing many-to-one and many-to-many threads, **Process Contention Scope, PCS**, occurs, because competition occurs between threads that are part of the same process. ( This is the management / scheduling of multiple user threads on a single kernel thread, and is managed by the thread library. )
- **System Contention Scope, SCS**, involves the system scheduler scheduling kernel threads to run on one or more CPUs. Systems implementing one-to-one threads ( XP, Solaris 9, Linux ), use only SCS.
- PCS scheduling is typically done with priority, where the programmer can set and/or change the priority of threads created by his or her programs. Even time slicing is not guaranteed among threads of equal priority.

## 6.5 Multiple-Processor Scheduling

- When multiple processors are available, then the scheduling gets more complicated, because now there is more than one CPU which must be kept busy and in effective use at all times.
- **Load sharing** revolves around balancing the load between multiple processors.
- Multi-processor systems may be **heterogeneous**, ( different kinds of CPUs ), or **homogenous**, ( all the same kind of CPU ). Even in the latter case there may be special scheduling constraints, such as devices which are connected via a private bus to only one of the CPUs. This book will restrict its discussion to homogenous systems.

### 6.5.1 Approaches to Multiple-Processor Scheduling

- One approach to multi-processor scheduling is **asymmetric multiprocessing**, in which one processor is the master, controlling all activities and running all kernel code, while the other runs only user code. This approach is relatively simple, as there is no need to share critical system data.
- Another approach is **symmetric multiprocessing**, **SMP**, where each processor schedules its own jobs, either from a common ready queue or from separate ready queues for each processor.
- Virtually all modern OSes support SMP, including XP, Win 2000, Solaris, Linux, and Mac OSX.

### 6.5.2 Processor Affinity

- Processors contain cache memory, which speeds up repeated accesses to the same memory locations.
- If a process were to switch from one processor to another each time it got a time slice, the data in the cache ( for that process ) would have to be invalidated and re-loaded from main memory, thereby obviating the benefit of the cache.
- Therefore SMP systems attempt to keep processes on the same processor, via **processor affinity**. **Soft affinity** occurs when the system attempts to keep processes on the same processor but makes no guarantees. Linux and some other OSes support **hard affinity**, in which a process specifies that it is not to be moved between processors.
- Main memory architecture can also affect process affinity, if particular CPUs have faster access to memory on the same chip or board than to other memory loaded elsewhere. ( Non-Uniform Memory Access, NUMA. ) As shown below, if a process has an affinity for a particular CPU, then it should preferentially be assigned memory storage in "local" fast access areas.

![](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_09_NUMA_CPU_Scheduling.jpg)

## 6.5.3 Load Balancing

- Obviously an important goal in a multiprocessor system is to balance the load between processors, so that one processor won't be sitting idle while another is overloaded.
- Systems using a common ready queue are naturally self-balancing, and do not need any special handling. Most systems, however, maintain separate ready queues for each processor.
- Balancing can be achieved through either **push migration** or **pull migration**:
    - **Push migration** involves a separate process that runs periodically, ( e.g. every 200 milliseconds ), and moves processes from heavily loaded processors onto less loaded ones.
    - **Pull migration** involves idle processors taking processes from the ready queues of other processors.
- Push and pull migration are not mutually exclusive.
- Note that moving processes from processor to processor to achieve load balancing works against the principle of processor affinity, and if not carefully managed, the savings gained by balancing the system can be lost in rebuilding caches. One option is to only allow migration when imbalance surpasses a given threshold.

### 6.5.4 Multicore Processors

- Traditional SMP required multiple CPU chips to run multiple kernel threads concurrently.
- Recent trends are to put multiple CPUs ( cores ) onto a single chip, which appear to the system as multiple processors.
- Compute cycles can be blocked by the time needed to access memory, whenever the needed data is not already present in the cache. ( Cache misses. ) In Figure 5.10, as much as half of the CPU cycles are lost to memory stall.
![Figure 6.10 - Memory stall.  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_10_MemoryStall.jpg)

- By assigning multiple kernel threads to a single processor, memory stall can be avoided ( or reduced ) by running one thread on the processor while the other thread waits for memory.
 
![Figure 6.11 - Multithreaded multicore system.  ](https://www2.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/images/Chapter6/6_11_MultithreadedMulticore.jpg)

- A dual-threaded dual-core system has four logical processors available to the operating system. The UltraSPARC T1 CPU has 8 cores per chip and 4 hardware threads per core, for a total of 32 logical processors per chip.

- There are two ways to multi-thread a processor:
    - **Coarse-grained** multithreading switches between threads only when one thread blocks, say on a memory read. Context switching is similar to process switching, with considerable overhead.
    - **Fine-grained** multithreading occurs on smaller regular intervals, say on the boundary of instruction cycles. However the architecture is designed to support thread switching, so the overhead is relatively minor.

- Note that for a multi-threaded multi-core system, there are two levels of scheduling, at the **kernel level**:
    - The OS schedules which kernel thread(s) to assign to which logical processors, and when to make context switches using algorithms as described above.
    - On a lower level, the hardware schedules logical processors on each physical core using some other algorithm.
    - The UltraSPARC T1 uses a simple round-robin method to schedule the 4 logical processors ( kernel threads ) on each physical core.
    - The Intel Itanium is a dual-core chip which uses a 7-level priority scheme ( urgency ) to determine which thread to schedule when one of 5 different events occurs.

/* Old 5.5.5 Virtualization and Scheduling ( Optional, Omitted from 9th edition )

- Virtualization adds another layer of complexity and scheduling.
- Typically there is one host operating system operating on "real" processor(s) and a number of guest operating systems operating on virtual processors.
- The Host OS creates some number of virtual processors and presents them to the guest OSes as if they were real processors.
- The guest OSes don't realize their processors are virtual, and make scheduling decisions on the assumption of real processors.
- As a result, interactive and especially real-time performance can be severely compromised on guest systems. The time-of-day clock will also frequently be off.
*/
