---
title: 操作系统学习笔记-ch5
date: 2021-10-17 17:14:06
tags: [笔记]
summary: hfx老师yyds！


---



#### 软中断信号（即信号）

- 目的是通知进程某一异步事件的发生。相当于Spigot编程中的EventListener，即出现了什么event，就相应执行其处理程序。
- 因为行为和中断是一样的，所以称为是软中断，其处理也是发出中断，然后执行相应的程序。
- 软中断信号的发出
  - 可以由内核发出
  - 也可以由外部设备经由内核发出（Ctrl+C）
  - 也可以由一个进程发给另一个进程（kill系统调用）

#### 软中断信号的查询和处理

- 进程PCB中，有类似于硬件中断向量表的结构。每一位对应一种软中断。
- 查询软中断：内核接收到中断信号，置PCB中对应的那一位为1。注意，由于是0或1，所以查询中断只能查到是否有此中断，而无法记录次数。
- 处理软中断：执行处理程序。
- 当进程从核心态转为用户态时，查询并处理软中断。
- 当进程进入执行，或从执行转为阻塞时，仅查询软中断。

#### signal()系统调用

- 用此函数来指定特定的中断如何处理。即给他指定一个中断处理函数，传入函数名即可。（或默认SIG_DFL）

```c
void handler(int) { //自定义的信号处理函数  }
signal(int signum, (void)handler); 
 //注册信号signum的自定义处理函数handler
```

- 传入SIG_IGN可以忽略此信号的处理。
- 如果在fork之前就signal了，那么子进程会继承父进程的处理函数。如果需要子进程有自己的处理函数，那么在子进程的代码中再signal一次。

#### kill()系统调用

- 可以通过kill来给其他进程发送信号。传入进程PID和信号即可。
- 比较常用的是父子进程之间发送信号。可以使用 `getppid()`来获得自己父进程的PID。

#### alarm()系统调用

- 进程调用此函数，往其传入`int`型参数，单位是秒。则这些秒后，这个进程自己会收到一个`SIGALARM`信号，如果预先定义了该种信号的处理函数，那么就可以实现定时作业。
- 特别地，`alarm(0)`系统调用返回一个距离下次`SIGALARM`信号还剩多少秒。

#### 共享内存使用流程

- A、B想要通信，然后A创建一段期望用于共享的空间Seg。但是此Seg还不能为A所访问，因为它没有被加到A的地址空间中。
- 之后，Seg加到A、B的地址空间中，此时就可以双方同时访问了（注意，进程同步需要做。）
- A、B不想通信了，Seg应当先从A、B的地址空间中删除，再由其`创建者`（也就是A）释放这段Seg。

#### 创建共享内存的代码实现

- `int shmid = shmget(参数)`
  - 尝试创建或获取一个共享内存区的`标识符`。
  - 传入key和size。
  - 可使用标志
    - IPC_CREAT：创建一个新的共享内存区。
      - 如果单独使用此标志，则如果key已存在，就返回其代指的共享内存的标识符，否则新建一个。
      - 这种机制的作用是：如果父子进程都调用shmget，则父子最终还是获得了同一段共享内存的标识符。
      - 如果不加IPC_CREAT，那么就不新建，而是尝试获取。
    - IPC_PRIVATE：创建一个只有本进程可以使用的共享内存区。（这种机制的作用是：只有自己这个进程和自己的子进程可以使用。**因为子进程可以继承父进程的共享内存单元。**）
- `shmptr=(char *)shmat( shmid, NULL, 0 )；`
  - 即`shared memory attach.`将创建的那段共享内存附接到本进程的**虚地址空间中**。因为它返回了一个void类型指针变量，此指针变量可以转换成任何类型，可以拿着这个指针直接去写这段内存。
    - 虚地址空间即程序数据段中相对于整个程序内存空间的偏移量。在汇编中，我们这样去表述一个数据；在C中，指针变量都是这个含义。
- `shmdt(shmptr)` 
  - 拿着共享内存的指针，表示我要把这段共享内存从我的程序上`detach`掉，即取消附接。
  - `所有使用`这段共享内存的进程在结束通信后都需要执行此命令，`以便下一步`，其创建者可以释放这段内存。

- `shmctl(shmid, IPC_RMID, NULL) `
  - 拿着共享内存的标识符，请求删除这段空间。
  - 这段共享内存的创建者来做这件事情。
- 注意， `shmat`对应 `shmdt`，且其凭证是一个指针。因此detach的时候传入那个指针。 `shmget`对应 `shmctl`，其凭证是一个`int`类型的共享内存标识符，因此在释放这段空间的时候，传入那个`int`标识符。

#### 线程和进程的隶属关系

- 由于线程没有资源，所以不可能脱离于进程而独立存在。你比如说，如果脱离了，连代码都没有了。
- 线程是进程的一个实体，运行在进程的上下文中。
  - 进程不可执行，因为进程本身不包含栈和寄存器的值。无法存储动态的上下文。因此只有线程是可执行的实体。
  - 进程只是为线程提供大环境。
- 不同进程的线程之间发生切换，才会导致进程上下文切换。

#### 线程是高效的

- 线程的创建、删除和切换所需的时间开销要远小于进程
- 线程由于隶属于进程，同一个进程中的线程本来就共享同样的地址空间，使得它们之间的通信甚至都不需要通过内核了。例如我的多线程外排序，直接使用全局变量就可完成通信。
  - 可见，共享内存也是旨在为进程之间提供这一种全局变量的通信概念。

#### 银行模型

- 用户线程相当于来办事的人，核心线程相当于坐在柜台里的职员，银行柜台及其内部金库相当于系统内核。
- 系统内核（金库）感受不到大厅里有多少人（感知不到用户线程的存在），而只能感知到有多少职员今天来上班了（只能感知到核心线程的存在）
- 大厅里的客户可以互相通信，互相协调谁先来办事，可以自由进出和离开，但是它们要办理业务只能通过银行职员（**用户级线程的管理（包括`创建、撤销`、调度（切换）、同步和`通信`等）是用户级别的，与核心无关；但是如果用户级线程要`运行`，则必须映射到一个核心线程上**）
  - 由于线程的管理完全可以用户态实现，所以有些线程库例如pthread只需要在用户态就能完成管理。我的多线程外排序只需要在用户态下使用全局变量即可完成通信。

#### 不支持多线程是什么意思

- 一句话理解：对于外部的一个进程，只有一个内核线程可供映射。因此即便是多CPU环境，也不可能把这些线程分开到多个CPU上并行执行，因为没得可映射的。
- 所谓不支持多线程，就是此OS对外界的感知不是以线程为单位，而是以**进程**为单位，或者说这个进程只有一个线程，所以就算外界有很多线程，也只能映射到这单个的内核线程上。
- 问题：当这仅有的线程阻塞了，这个用户进程就阻塞了。

#### 内核线程的管理

- 内核线程Supported by the Kernel and managed by the Kernel.
  - Supported by the Kernel
    - OS只会为核心线程分配CPU等资源。
  - Managed by the Kernel
    - 线程的创建、删除、切换、同步、通信都由内核完成，通过系统调用实现。
    - 且只有内核线程是内核可感知的。
    - 线程控制块TCB：在内核空间中创建，可以类比PCB。
      - PCB是内核感知进程的唯一方式，所以TCB也是内核感知内核线程的唯一方式。
      - 内核对内核线程的管理通过TCB完成。

#### many-to-many模型下的三种情况

- 如果核心线程数量小于CPU的个数
  - 有些CPU会空闲

- 如果核心线程数量等于CPU的个数
  - 所有CPU可能会充分利用，但若有的线程阻塞，则这些CPU会空闲

- 如果核心线程数量大于CPU的个数
  - 若有的线程阻塞，其他线程会被调度到这些CPU上执行，CPU会得到充分利用

#### Unix下的多线程规范

```c
pthread_create(…); //创建一个线程
pthread_join(tid,…); //等待线程结束,进行数据同步
```

#### 另一个角度理解抢占式调度

- 将进程分割成了多个CPU执行期的调度方式.

#### 关于FCFS

- 护航效应导致其仅对长CPU时间的进程有利.
- 算法的调度效果和进程的顺序直接相关.可以类比quickSort的算法复杂度与顺序的关系.
- FCFS是例如优先级调度,RR调度的基本算法.因为例如当遇到相等优先级时,就会采用FCFS蒙混过关.
- 注意,只有在就绪队列中的PCB**才有被调度的资格**.

#### 关于SJF

- 对于抢占式SJF，当一个新的PCB要进入就绪队列，就会先比较其下一段CPU时间和当前执行的进程的剩余CPU时间。如果前者更短，直接抢占。（对于其他的抢占式，例如抢占式优先级调度，当一个新的PCB到来，**也会执行类似的操作。这是抢占式调度的显著特点。**）
- 关键问题是知道就绪队列中有什么进程在等待，它们的下一段CPU时间是多少。
- SJF有利于短作业，不利于长作业。

#### 高响应比调度算法

- 高相应比调度算法实际上是**采用响应比作为优先级**的优先级调度算法。
- 响应比的公式：`（等待时间/CPU时间）+1`
  - 因为等待时间是分子，所以式永远有意义，且>=1.
  - 对于短进程和长进程，如果它们都在就绪队列中等待，则随着时间的增长，短进程的相应比增长得更快，所以这个算法仍然照顾了短进程。（因为照顾短进程能获得更高的吞吐量和更小的平均等待时间，这个在上一篇博客中有推导）
  - 但是就算是长进程，如果其等待时间非常长，也有机会使其优先级超过短进程。因此也能合理消除饥饿现象。

#### 顺序执行和并发执行

- 顺序执行：一个程序，其各个步骤之间的偏序关系是在程序设计时就确定了的。
- 并发执行：但是，如果这个程序有几个线程，而偏序关系又是跨线程的，那么谁也不能保证这个偏序关系会不会因为并发而被打乱。因为线程之间的调度顺序由操作系统的调度程序决定。
  - 虽然调度顺序由OS决定，但是我们可以控制它虽然被调度，但是不开始执行啊。
  - 因此，实际上，执行顺序仍由应用程序自行协商决定。

#### 临界资源

- **临界资源是要求互斥访问的资源**。
- 例如打印机，注册表，文件。

#### 临界区

- 程序的一个代码段。特点是，在该段内，该程序**访问了临界资源**。
- **为了保证**临界资源的互斥访问，要求对同一临界资源，同一时间**只能有一个**进程在其对应的临界区中。

#### 伯恩斯坦条件

- 读写、写写操作不能并发访问同一资源，否则会出问题。

#### Race condition

**Race condition**: The situation where several processes access，and manipulate shared data concurrently. The final value of the shared data depends upon which process finishes last.

- Race condition是一个状况，并不应该像书上那样翻译成“条件”。其同义词应该是situation才对。
- Race condition就是指多个进程并发**访问和操作**同一个共享数据的情况。该数据最终取值取决于**最后一个**完成访问和操作的进程。
- 如何解决Race condition？**——引入进程同步的概念。**

#### 原子操作和原语

- 原子操作在核心态下实现，并常驻内存。
- 原语是指一段程序。这段程序实现的功能是一个原子操作。
