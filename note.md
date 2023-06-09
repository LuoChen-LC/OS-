# 引论

## 1.1.1 操作系统的概念

**什么是OS**：

**1. 系统资源的管理者：**

提供的功能：进程管理、内存管理、文件管理、设备管理

目标：安全、高效

**2. 向上层提供方便易用的服务**

封装思想：只需对操作系统发出命令就可以使用硬件功能

提供的服务：

GUI：图形化用户接口

命令接口：交互式命令接口和批处理命令接口

程序接口：系统调用来使用程序接口。只能用代码调用

统称为用户接口

**3. 最接近硬件的软件**

实现对硬件机器的扩展，通常把覆盖了软件的机器称为扩充机器或虚拟机

## 1.1.2 操作系统的特征

**并发**：宏观上同时发生，微观上交替发生

单核CPU同一时刻只能执行一个程序，各个程序只能并发运行

多核CPU可以并行执行多个程序

**共享**

资源共享：

互斥共享：一个时间段内只允许一个进程访问该资源

同时共享：例如用QQ和微信发送文件，“同时”对硬盘资源进行访问

并发和共享互为存在条件

**虚拟**：空分复用技术（虚拟内存）和时分复用技术（虚拟CPU）

**异步**：资源的有限性，多道程序并发性导致了异步，即程序运行是走走停停的

## 1.2 操作系统的发展与分类

**手工操作阶段**

纸袋机

主要缺点：用户独占全机，**人机速度矛盾**导致资源利用率极低

**单道批处理系统**

脱机输入输出技术，外围机加磁带

监督程序

优点：缓解了一定程度的人机速度矛盾

缺点：内存中仅能有一道程序运行，CPU大量时间等待IO完成，资源利用率低

**多道批处理系统**

主要优点：并发，共享，资源利用率大幅提升

主要缺点：用户响应时间长，没有人机交互功能

**分时操作系统**

时间片为单位轮流为用户服务

优点：解除了人机交互问题

缺点：不能优先处理一些紧急任务

**实时操作系统**

及时性可靠性

硬实时系统：必须在绝对严格的规定时间内完成

软实时系统：允许偶尔违反时间规定

**其他操作系统**：网络操作系统，分布式操作系统，个人计算机系统

## 1.3.1 操作系统的运行机制

内核程序：执行特权指令

应用程序：执行非特权指令

CPU的两种状态：内核态（管态），用户态（ 目态）

程序状态寄存器（PSW）

内核（kernel）是操作系统最核心的部分，有很多内核程序组成

内核态到用户态：执行一条特权指令修改PSW

用户态到内核态：中断引发，硬件自动完成

## 1.3.2 中断和异常

**中断的作用**

内核夺回CPU使用权

**中断的类型**

**内中断（异常）**：与当前执行的指令有关，来源于CPU内部，执行指令时都会检查是否有异常发生

陷阱（陷入指令，实现系统调用），故障（如缺页故障），终止（无法修复，直接终止）

例子：终止：在用户态执行特权指令，执行除法指令发现除数为0，

**外中断（中断）**：与当前执行的指令无关，来源于CPU外部，每个指令周期末尾检查是否有外中断需要处理

例子1，时钟中断：时钟部件每隔一个时间片给CPU一个时钟中断

例子2，IO中断：由输入输出设备发出的中断信号

**基本原理**

根据中断信号的不同选择不同的中断处理程序

**中断向量表**

**中断处理程序**：一种内核程序

## 1.3.3 系统调用

**什么是系统调用**：应用程序可以通过系统调用请求获得操作系统内核的服务

系统调用和库函数的区别：编程语言会将系统调用封装成库函数。如“创建一个新文件”就需要系统调用

**为什么系统调用是必须的？**

内核对共享资源进行统一管理，并向上提供“系统调用”。内核会对应用程序发出的请求进行协调处理

**什么功能需要系统调用实现**

与共享资源有关的操作、会直接影响到其他进程的操作。

**系统调用的过程**

传参指令：将系统调用需要的参数放到通用寄存器

陷入指令（trap，访管指令）：非特权指令，引发内中断，在用户态运行。

根据寄存器的参数判断系统调用服务，执行相应的内核程序，该过程在内核态运行。

返回应用程序

## 1.4 操作系统体系结构

内核程序

进程管理，存储器管理，设备管理等

时钟管理，中断处理，原语（设备驱动，CPU切换等，具有原子性）

**宏内核**：保留所有功能

优点：高性能，内核内部各种功能可以直接相互调用

缺点：内核庞大功能复杂，难以维护；某个功能模块出错导致整个系统崩溃

**微内核**：只保留与硬件最接近的部分功能

优点：内核功能少，结构清晰，易于维护；某个功能模块出错不会导致整个系统崩溃

缺点：需要频繁地在内核态和用户态切换，性能低；需要内核的“消息传递”来间接通信。

CPU的状态转换是有成本的

**分层结构**

特性和思想：内核分多层，每层可单向调用更低一层提供的接口

优点：便于调试和验证；易扩充和易维护

缺点：仅可调用相邻底层，难以定义各层的边界；效率低，不可跨层调用，系统调用执行时间长

**模块化**

内核  = 主模块 + 可加载内核模块

主模块：只负责核心功能，如进程调度，内存管理

可加载内核模块：可以动态加载新的模块到内核，而无需重新编译整个内核（例如驱动程序）

优点：易于维护，多模块同时开发；动态加载新的内核模块；无需才用消息传递，效率高

缺点：接口定义未必合理，实用；难以调试和验证。

**外核**

内核负责进程调度、通信等功能，外核负责为用户进程分配未经抽象的硬件资源，且由外核负责保证资源使用安全

优点：更灵活的使用硬件资源；减少了虚拟硬件资源的映射，提升效率

缺点：降低了系统的一致性；变得更复杂。

## 1.5 操作系统引导

开机的时候如何让操作系统运行起来

磁盘的第一块：主引导记录（MBR），包含磁盘引导程序和分区表

活动分区（主分区，安装操作系统的分区）：引导记录（PBR）负责找到启动管理程序、根目录、其它

主存由RAM和ROM（BIOS），其中包含ROM引导程序

过程：

1. CPU从一个特定的主存地址开始取指令，执行ROM中的引导程序
2. 将磁盘的第一块读入内存，执行磁盘引导程序，扫描分区表
3. 从活动分区读入分区引导记录，执行其中的程序
4. 从根目录下找到完整的操作系统初始化程序并执行，完成开机

## 1.6 虚拟机

VMM：虚拟机管理程序

第一类直接运行在硬件上，运行在内核态，可以使用最高级特权指令，但是上层的虚拟内核空间在用户空间。

第二类运行在宿主操作系统上（平时使用的方式）

|                    |                                      |                                                                 |
| ------------------ | ------------------------------------ | --------------------------------------------------------------- |
| 对物理资源的控制权 | 直接运行在硬件之上，能直接控制和分配 | 运行在宿主操作系统上，依赖于宿主操作系统分配物理资源            |
| 资源分配           | 类似于外核的分配方式                 | 相当于虚拟的虚拟，分配到的是虚拟内存                            |
| 性能               | 好                                   | 差，需要中介                                                    |
| 可支持的虚拟机数量 | 多                                   | 少                                                              |
| 可迁移性           | 差                                   | 好                                                              |
| 运行模式           | 可以执行最高级特权指令               | 部分运行在用户态，内核态（VM驱动），发出的系统调用会被VMM截获。 |

# 进程与线程

## 2.1.1 进程的概念、组成、特征

**概念**

程序是静态的，进程是动态的

**组成**

PCB：进程控制块，进程存在的唯一标志

保存的信息：进程描述信息；进程控制和管理信息；资源分配清单；处理机相关信息

程序段：包含程序代码，指令序列

数据段：包含运行过程产生的各种数据

一个进程实体由PCB、程序段、数据段组成，进程就是进程实体的运行过程，是系统进行**资源分配**和**调度**的一个独立单位

**特征**

动态性：最基本特性

并发性

独立性

异步性：进程同步机制解决异步问题

结构性

## 2.1.2 进程的状态与切换，进程的组织

创建态、就绪态、运行态、阻塞态、终止态

3种基本状态

Running：占有CPU，并在CPU上运行

Ready：具备运行条件，但是CPU没有空闲

Blocked：等待某一事件而暂时不能运行

PCB中有一个变量state表示进程的当前状态

**进程的组织方式**

链接方式：执行指针

就绪队列指针

阻塞队列指针

还有索引方式

## 2.1.3 进程控制

进程控制就是实现进程状态的转换

用原语实现进程控制：执行具有原子性，运行不能被打断

实现原子性：关中断指令和开中断指令

相关的原语

**进程的创建**：申请空白PCB、分配资源，初始化PCB，插入就绪队列

用户登录（系统初始化），应用请求，系统调用，批处理作业初始化

**进程的终止**：撤消原语

正常结束，异常结束，外界干预

**进程的阻塞和唤醒**

阻塞原语

唤醒原语

**进程的切换**

切换原语：需要将运行环境存入PCB

保存运行环境：保存一些必要的寄存器信息

PC：程序计数器

IR：指令寄存器

通用寄存器

## 2.1.4 进程通信

数据交互

**共享存储**

申请共享存储区

保证互斥访问

基于数据结构的共享

基于存储区的共享

**消息传递**

格式化的消息：消息头 消息体

两个原语：发送原语 接收原语

直接通信方式

间接通信方式

PCB中的消息队列，信箱

**管道通信**

单向管道：pipe文件，在内存中开辟一个大小固定的内存缓冲区，先进先出

半双工通信，全双工通信

## 2.1.5 线程的概念

线程是程序执行流的最小单位，调度的基本单位

**具备的属性**：

提升了并发性

系统开销减小

每个线程都有线程ID，线程控制块TCB

三种基本状态

几乎不拥有系统资源

## 2.1.6 线程的实现方式和多线程模型

**用户级线程 ULT**

由用户管理线程，不需要CPU完成切换，但是操作系统不能意识到用户级线程的存在

优点：系统开销小，效率高

缺点：当一个用户级线程被阻塞了，整个进程都会被阻塞，并发度并不高。不可以并行运行

**内核级线程 KLT**

由操作系统管理，需要切换到内核态才能切换

优点：并发能力强

缺点：线程的管理成本高

**多线程模型**

一对一模型

多对一模型：多个线程映射到一个内核级线程

多对多模型：n个用户级线程映射到m个内核级线程（n >= m）

## 2.1.7 线程的状态与转换

线程的状态一般只考虑三种基本状态的切换

从运行态到阻塞态：等待某一事件

从阻塞态到就绪态：等待的事件发生

从就绪态到运行态：被调度程序选中

从运行态到就绪态：时间片用完

**线程的组织与控制**

TCB：TID、PC、寄存器，堆栈指针（保存函数调用信息，局部变量）、线程运行状态、优先级

线程切换时要保存和恢复：PC，其他寄存器，堆栈指针

多个线程组织成线程表

## 2.2.1 调度的概念、层次

**基本概念**

由于资源有限，按照某种**规则**运行程序

**层次**：

作业：一个具体的任务

用户向系统提交一个作业 = 用户让操作系统启动一个程序

高级调度（作业调度）：按照一定的规则从外存的作业后备队列中挑选一个作业进入内存，并创建进程；面向作业的调度

低级调度（进程调度）：从就绪队列选择一个进程，将CPU分配给它；最基本的一种调度，频率很高

中级调度（内存调度）：将进程调出外存，挂起状态，将进程PCB组织成挂起队列；面向进程的调度

**进程的挂起态**

就绪挂起：从就绪态挂起和激活

阻塞挂起：从阻塞态挂起和激活

## 2.2.2 进程调度的时机、切换与过程、方式

时机：

主动放弃：进程终止，发生异常，主动请求阻塞

被动放弃：时间片用完，有更紧急的事需要处理，更高优先级的进程

不能进行调度：

处理中断

内核程序临界区（访问某种内核数据结构）不能进行调度与切换，如进程的就绪队列

原语

**切换与过程**

对原来运行进程各种数据的保存

对新的进程各种数据的恢复

进程调度和切换是有代价的

**方式**

非抢占式：实现简单，但无法及时处理紧急任务

抢占式：可由操作系统剥夺当前进程的CPU使用权

## 2.2.3 调度器和闲逛进程

**调度程序**

调度算法，时间片大小

调度时机：创建新进程，进程退出，进程阻塞，IO中断

非抢占式：只有运行阻塞或者退出才会触发

抢占式：每个时钟中断会触发调度程序工作

**闲逛进程**

没有就绪进程时

优先级最低

0地址指令，周期末尾例行检查时钟中断

能耗低

## 2.2.4 调度算法的评价指标

**CPU利用率**

利用率 = 忙碌时间/总时间

**系统吞吐量**

单位时间完成作业的数量

**周转时间**

从提交到完成的时间间隔

周转时间 = 作业完成时间 - 作业提交时间

平均周转时间 = 各作业之和 / 作业数

带权周转时间 = 作业周转时间 / 实际运行时间

**等待时间**

建立后等待服务时间 = 周转时间 - 运行时间 - IO操作时间

**响应时间**

从用户提交请求到首次产生响应所用的时间

## 2.2.5 调度算法

饥饿：某个进程长时间得不到服务

**先来先服务FCFS**

非抢占式算法，按照到达的先后顺序调度

优点：公平，时间简单

缺点：对长作业有利，对短作业不利

不会导致饥饿

**短作业优先SJF（短进程优先SPF）**

最短的作业进程优先得到服务

一般分为非抢占式和抢占式（最短剩余时间优先算法SRTN）

 在进程几乎同时到达时，采用SJF调度算法的平均等待时间、平均周转时间最少

不加前提条件时，SRTN的平均等待时间、平均周转时间最少

优点：时间少

缺点：对短作业有利，对长作业不利

会导致长作业饥饿

**高响应比优先算法HRRN**

响应比 = （ 等待时间 + 服务时间 ）/ 服务时间

响应比最高的先运行

非抢占式

优点：综合考虑了等待和运行时间

不会导致饥饿

一般使用于早期的批处理系统

**时间片轮转调度算法RR**

分时操作系统更关心响应时间

轮流执行一个时间片，用完后剥夺CPU，重新排队

用于进程调度

抢占式算法，由时钟中断来通知CPU时间

时间片太大会退化为先来先服务算法，增大进程响应时间

时间片太小会导致进程切换过于频繁

优点：公平，适用于分时操作系统

缺点：高频率的进程切换开销大

**优先级调度算法**

选择优先级最高的作业或进程

抢占式和非抢占式

优先级组织就绪队列

动态改变优先级:

系统进程高于用户进程

前台进程高于后台进程

偏好IO进程（IO设备可以与CPU并行工作)

缺点：若不断有高优先级到达，可能导致饥饿

**多级反馈队列调度**

可抢占式进程调度算法

流程：

设置多级就绪队列，优先级从高到低，时间片从小到大

新进程先进入第一级队列，按照FCFS，若时间片用完会进入下一级队列队尾

只有K级队列为空才会为K+1级队头的进程分配时间片

发生抢占时当前进程会放回原级队列队尾

优点：公平，很快得到响应，短进程短时间完成，不必估计运行时间。

可能会导致饥饿：不断有短作业进入

以上三种适用于**交互式系统**

**多级队列调度**

按照进程类型设置多个队列，固定优先级，划分时间片

## 2.3.1 进程同步、互斥

进程具有异步性：独立的、不可预知的速度运行

进程同步是建立某种先后顺序

一个时间段内只允许一个进程使用的资源称为**临界资源**，对临界资源的访问需要**互斥访问**

互斥访问逻辑上四个部分：进入区，临界区，退出区，剩余区。上锁解锁过程

遵循原则：

1. 空闲让进：临界区空闲时，可以允许一个请求进入临界区的进程立即进入临界区
2. 忙则等待：当已有进程进入临界区时，其他试图进入临界区的进程必须等待
3. 有限等待：对请求访问的进程，应保证能在有限实践内进入临界区（保证不会饥饿）
4. 让权等待：当进程不能进入临界区时，应立即释放CPU，防止进程忙等待

## 2.3.2 进程互斥的软件实现方法

<<<<<<< HEAD:note.md
**单标志法**

每个进程进入临界区的权限只能被另一个进程赋予

```c++
int turn = 0; // 表示当前允许进入临界区的进程号
P0(){
	while(turn != 0);//进入区
	critical seciton;//临界区
	turn = 1;//退出区
	remainder seciton;//剩余区
}
P1(){
	while(turn != 1);
	critical seciton;
	turn = 0;
	remainder seciton;
}
```

问题：违背空闲让进的原则

**双标志先检查**

设置一个布尔型数组，标记各进程想要进入临界区的意愿。

```c++
bool flag[2];
flag[0] = false;
flag[1] = false;
P0(){
	while(flag[1]);
	flag[0] = true;
	critical section;
	flag[0] = false;
	remainder seciton;
}
P1(){
	while(flag[0]);//如果P0想要进入临界区，P1就一直循环等待
	flag[1] = true;//标记P1想进入临界区
	critical section;//临界区
	flag[1] = false;//访问完，修改P1不想使用临界区
	remainder seciton;
}
```

违反“忙则等待”原则，原因是检查和上锁两个处理操作不具有原子性

**双标志后检查**

与先检查相比是先上锁再检查

```c++
P0(){
    flag[0] = true;
    while(flag[1]);
    ...
}
```

违背了空闲让进和有限等待原则，产生饥饿

**Peterson算法**

```c++
bool flag[2];//表示进入临界区意愿
int turn = 0;//表示优先让哪个进程进入临界区
P0(){
    flag[0] = true;
    turn = 1;
    while(flag[1] && turn == 1);
	critical seciton;
	flag[0] = false;
    remainder section;
}
P1(){
	flag[1] = true;
	turn = 0;
	while(flag[0] && turn == 0);
	critical seciton;
	flag[0] = false;
	remainder section;
}
```

未遵循让权等待

=======

>>>>>>> parent of 01bcd2a... 5-23-16-02:笔记.md
>>>>>>>
>>>>>>
>>>>>
>>>>
>>>
>>

## 2.3.3 进程互斥的硬件实现方法

**中断屏蔽**

利用开关中断指令实现

优点：简单高效

缺点：不适用于多CPU；只适用于内核进程；只能运行在内核态；

**TSL指令**

用硬件实现的，以下是C语言描述的逻辑

```c++
//lock表示当前临界区是否被加锁
bool TestAndSet(bool *lock){
	bool old;
	old = *lock;//用来存放lock原来的值
	*lock = true;//无论是否加锁，都设为true
	return old;//返回lock原来的值
}
while(TestAndSet(&lock))；//上锁并检查
critical section;
lock = false;//解锁
remainder section;
```

优点：实现简单，无需严格检查是否有逻辑漏洞；适用于多CPU环境

缺点：不满足让权等待原则，暂时无法进入临界区的进程会占用CPU并循环执行TSL指令，从而导致“忙等”

**Swap指令或XCHG指令**

逻辑上与TSL指令没有太大区别

```c++
Swap(bool *a, bool *b){
    bool temp;
    *a = *b;
    *b = temp;
}
bool old = true;
while(old == true){Swap(&lock, &old);}
critical section;
lock = false;
remainder section;
```

## 2.3.4 互斥锁

互斥锁：一个进程在进入临界区获得锁，退出时释放锁

```c++
bool available;//锁是否可用
acquire(){
    while(!available);//忙等待
    available = false//获得锁
}
release(){
    available = true;//释放锁
}
```

特性：

1. 需忙等：违法让权等待
2. 优点：等待期间不用切换上下文；若上锁时间很短，则等待代价很低
3. 常用于多处理器系统；
4. 忙等过程不可能解锁

## 2.3.5 信号量机制

一对原语来对信号量进行操作，可以表示系统中某种资源的数量

一对原语：`wait(S)`和 `signal(S)`，简称为P、V操作

**整型信号量**

用整数表示某种资源的数量，只能进行初始化、P、V操作

```c++
int S = 1;
void P(S){//相当于进入区
    while(S <= 0);//忙等
    S = S - 1;
}
void V(S){//相当于退出区
    S = S + 1;
}
```

不满足“让权等待”

**记录型信号量**

P操作意味着请求一个单位的该类资源，当资源不足会调用block原语进行自我阻塞

V操作意味着释放一个单位的该类资源，当资源依然小于等0就唤醒等待队列的进程

```c++
typedef struct{
    int value;//剩余资源数
    struct process  *L;//等待队列
}semaphore;

void P(S){
    S.value--;
    if(S.value < 0){
        block(S.L);
    }
}
void V(S){
    S.value++;
    if(S.value <= 0){
        wakeup(S.L);
    }
}
```

## 2.3.6 用信号量实现进程互斥、同步、前驱关系

信号量的值 = 资源的剩余数量

**实现进程互斥**

互斥信号量 `mutex`,初值为1

对不同的临界资源需要不同的互斥信号量

P、V操作需要成对出现

```c++
semaphore mutex = 1;
P1(){
	P(mutex);
    临界区;
    V(mutex);
}
```

**实现进程同步**

进程同步：要让各并发进程按要求有序地运行

保证一前一后的操作

设置同步信号量 `S`，初值为0；

前操作之后执行V操作

后操作之前执行P操作

**实现进程的前驱关系**

每一对前驱关系都需要一个同步信号量，初值都为0

前V后P

## 2.3.7 生产者消费者问题

PV操作题目的分析步骤；

1. 关系分析：分析各个进程的同步和互斥关系
2. 整理思路：根据各进程的流程确定P、V操作的大致顺序
3. 设置信号量：确定信号量初值。

```c++
semaphore mutex = 1;//互斥访问
semaphore empty = n;//空闲缓冲区
semaphore full = 0;//非空闲缓冲区

producer(){
    while(1){
        P(empty);
        P(mutex);
        临界区;
        V(mutex);
        V(full);
    }
}
consumer(){
    while(1){
        P(full);
        P(mutex);
        临界区;
        V(mutex);
        V(empty);
    }
}
```

mutex的P操作前会导致死锁，因此实现互斥的P操作一定要在实现同步的P操作之后

两个V操作顺序可以交换

从事件的角度来考虑同步问题

## 2.3.8 吸烟者问题

互斥关系：桌子抽象为容量为1的缓冲区

同步关系：组合一则第一个抽烟者取走东西；组合二则第二个抽烟者取走东西；组合三则第三个抽烟者取走东西；取走东西后才能放东西

```c++
semaphore offer1 = 0;
semaphore offer2 = 0;
semaphore offer3 = 0;
semaphore finish = 0;//是否完成
int i = 0;//实现轮流吸烟
provider(){
    while(1){
        if(i == 0){
            组合一;
            V(offer1);
        }
        if(i == 1){
            组合二;
            V(offer2);
        }
        if(i == 2){
            组合三;
            V(offer3);
        }
        i = (i + 1) % 3;
        P(finish);
    }
}
smoker1(){
    while(1){
        P(offer1);
        临界区;
        V(finish);
    }
}
...
```

整型变量实现轮流关系

若一个生产者生产多种产品，V操作应该在对应事件发生之后的位置

## 2.3.9 读者写者问题

注意读进程与消费者进程的区别

多个读者可同时访问共享数据：通过一个整型信号量和一个互斥信号量实现同时访问

核心思想在于设置了一个计数器对第一个进程和最后一个进程从而做出不同的处理

读者优先：

```c++
semaphore rw = 1;//共享文件的互斥访问
int readcount = 0;//记录当前有几个读进程在访问文件
semaphore rm = 1;
writer(){
    while(1){
        P(rw);
        write;
        V(rw);
    }
}
reader(){
    while(1){
        P(rm);
        if(count == 0){//由第一个读进程负责加锁
            P(rw);
        }
        count++;
    	V(rm);
    	read;
    	P(rm);
    	count--;
    	if(count == 0){//由最后一个读进程负责解锁
       	 	V(rw);
   	 	}
        V(rm);
    }
}
```

读写公平：

```C++
semaphore mutex = 1;//共享文件的互斥访问
semaphore rw = 1;//用于实现读写公平
int readcount = 0;//记录当前有几个读进程在访问文件
semaphore rmutex = 1;
writer(){
    while(1){
        P(rw);
        P(mutex);
        write;
        V(mutex);
        V(rw);
    }
}
reader(){
    while(1){
        P(rw);
        P(rmutex);
        if(count == 0){//由第一个读进程负责加锁
            P(mutex);
        }
        count++;
    	V(rmutex);
        V(rw);
    	read;     
    	P(rmutex);
    	count--;
    	if(count == 0){//由最后一个读进程负责解锁
       	 	V(mutex);
   	 	}
        V(rmutex);
    }
}
```

写者优先：需要让读进程和写进程挂在不同的信号量的等待队列上

```c++
semaphore mutex = 1;//共享文件的互斥访问
semaphore w = 1;//用于实现写优先
int readcount = 0;//记录当前有几个读进程在访问文件
int writecount = 0;
semaphore rmutex = 1;
semaphore wmutex = 1;
writer(){
    while(1){
        P(wmutex);
        if(writecount == 0){
            P(w);
        }
        writecount++;
        V(wmutex);
        P(mutex);
        write;
        V(mutex);
        P(wmutex);
        writecount--;
        if(writecount == 0){
            V(w);
        }
        V(wmutex); 
    }
}
reader(){
    while(1){
        P(w);
        P(rmutex);
        if(count == 0){//由第一个读进程负责加锁
            P(mutex);
        }
        count++;
    	V(rmutex);
        V(w);
    	read;     
    	P(rmutex);
    	count--;
    	if(count == 0){//由最后一个读进程负责解锁
       	 	V(mutex);
   	 	}
        V(rmutex);
    }
}
```

## 2.3.10 哲学家就餐问题

多个临界资源

互斥信号量数组

1. 对哲学家进程施加一些限制条件：最多允许四个哲学家同时进餐

   2. 要求奇数号哲学家先拿左边的筷子，然后再拿右边的筷子；偶数号哲学家相反
   3. 仅当一个哲学家左右两支筷子都可用时才允许他抓起筷子

```c++
semaphore chopstick[5]={1,1,1,1,1};
semaphore S = 4;
Pi(){
    while(1){
        P(S);
        P(chopstick[i]);
        P(chopstick[(i+1)%5]);
        eat;
        V(chopstick[i]);
        V(chopstick[(i+1)%5]);
        V(S);
      
    }
}//第一种想法
Pi(){
    if(i%2 == 0){
        P(chopstick[(i+1)%5]);
        P(chopstick[i]);
        eat;
        V(chopstick[i]);
        V(chopstick[(i+1)%5]);
    }
    if(i%2 == 1){
        P(chopstick[i]);
        P(chopstick[(i+1)%5]);
        eat;
        V(chopstick[(i+1)%5]);
        V(chopstick[i];
    }
}
```

## 2.3.11 管程

信号量机制存在的问题：编写程序困难，易出错

管程：高级的同步机制

**定义和基本特征**

组成：

1. 局部于管程的共享数据结构说明
2. 对数据结构进行操作的一组过程
3. 初始化的语句

类似于类的定义

基本特征：

1. 局部于管程的数据只能被局部于管程的过程所访问
2. 一个进程只有通过调用管程内的过程才能进入管程访问共享数据
3. 每次仅允许一个进程在管程内执行某个内部过程

互斥的实现由编译器负责

使用条件变量及等待唤醒操作实现同步

体现了封装的思想

## 2.4.1 死锁的概念

# 内存管理

## 3.1.1 内存的基础知识

程序执行前需要放到内存中才能被CPU处理

每个地址对应一个存储单元，按字节编址（1B），按字编址（字长）

常用的数量单位

4GB = 2^32^ B：2^10^ = 1K;2^20^ = 1M; 2^30^ = 1G；1B = 8 bit

指令的工作原理：基于”地址“工作，操作码 + 若干参数

逻辑地址和物理地址

从写程序到程序运行

1. 编辑源文件
2. 编译：生成目标模块；翻译为机器语言
3. 链接：由目标模块生成装入模块，链接后形成完整的逻辑地址
4. 装入：装入内存形成物理地址

三种链接方式：

1. 静态链接：装入前链接成一个完整装入模块
2. 装入时动态链接
3. 运行时动态链接

三种装入方式：

1. 绝对装入：单道程序阶段
2. 可重定位装入：多道批处理操作系统
3. 动态运行时装入

## 3.1.2 内存管理的概念

**内存空间的分配与回收**

**内存空间的扩充**：虚拟性的体现

**地址转换**：逻辑地址与物理地址的转换

**内存保护**：上下限寄存器；基址寄存器（重定位寄存器，存放起始物理地址）和限长寄存器（界地址寄存器最大逻辑地址）

## 3.1.3 覆盖与交换

用于内存空间的技术

**覆盖技术**

将同一程序分段，常用段放在固定区，不常用放在覆盖区

必须由程序员声明覆盖结构；对用户不透明

**交换技术**：对于不同进程之间的内存与外存的交换；中级调度

磁盘分为文件区和对换区；对换区的IO速度更快

缺页，缺页率

优先换出阻塞进程；低优先级进程；考虑进程的驻留时间

PCB会常驻内存

## 3.1.4 连续分配管理方式

**单一连续分配**：

系统区和用户区；

内存中只能有一道用户程序

优点：实现简单；无外部碎片；可以采用覆盖技术；不一定需要采取内存保护

缺点：只能用于单用户、单任务的操作系统；有内部碎片；存储器利用率极低；

**固定分区分配**

用户空间划分为若干分区；

又分为分区大小相等或不相等；

一个数据结构：分区说明表，每个表项包括大小、起始地址、状态

优点：实现简单；无外部碎片

缺点：太大的程序不能满足要求；会产生内部碎片

**动态分区分配**

根据进程大小动态建立分区；

空闲分区表：分区号、大小、起始地址

空闲分区链：双向链表

动态分区分配算法

内部碎片：某进程的内存区域有些部分没有用上

外部碎片：空闲分区太小难以利用

紧凑技术来解决外部碎片

## 3.1.5 动态分区分配算法

**首次适应算法**

每次从低地址开始，找到第一个能满足大小的空闲分区

**最佳适应算法**

找出能容纳进程的最小的空闲区

会产生很多外部碎片

**最坏适应算法**

总是分配最大的可用空闲区

**邻近适应算法（next fit）下次适配算法**

每次从上次查找结束的位置开始查找

## 3.1.6 基本分页存储管理的概念

将内存空间分为一个个大小相等的分区，每个分区就是一个页框（都有页框号）

将进程的逻辑地址空间也分为与页框大小相等的一个个部分，每个部分称为一个页或者页面（都有页号）

页面和页框都有一一对应的关系

页表：每个进程建立一个页表，存在PCB中；一个页表项包含页号和页框号

每个页表项占多少字节？

Eg：物理内存大小4GB，页面大小4KB；至少需要3B来表示块号，由于页号是隐含的，每个页表项占3B，存储整个页表需要

3*（n+1）B

页号 = 逻辑地址 / 页面大小

页内偏移量 = 逻辑地址 % 页面大小

通过页号查询页表得到起始地址

实际的物理地址 = 起始地址 + 页内偏移量

结论：如果页面大小刚好是2的整数幂，则只需把页表中记录的物理块号拼接上页内偏移量就能得到对应的物理地址

## 3.1.7 基本地址变换机构

页表寄存器PTR，页表起始地址F和页表长度M（总共有几个页表项）

可能发生越界中断

对于一个页面大小为L，逻辑地址为A到物理地址E的变换过程

1. 根据逻辑逻辑地址计算出页号 P = A / L、页内偏移量 W = A % L
2. 判断页号是否越界，页表长度M；P>=M时就会越界
3. 访问内存中的页表、找到页号对应的页表项（F + P * 页表项长度），确定内存块号b
4. 用内存块号和页内偏移量得到物理地址 E = b * L + W；
5. 访问目标内存单元

## 3.1.8 具有快表的地址变换机构

TLB：联想寄存器，是一种高速缓存，用来存放最近访问的页表项副本

当进程切换快表是需要清除的，快表是专门的硬件

在查询页表前先访问TLB，根据是否命中决定是否访问

若快表命中，只需要一次访存，未命中则需要两次访存

**局部性原理**

时间局部性：某条指令可能不久会再次被访问

空间局部性：某个存储单元的附近可能被再次访问

## 3.1.9 两级页表

页表必须连续存放，需要占用很多个连续的页框

没必要让整个页表常驻内存

 一级页号 二级页号 页内偏移量

顶级页表：存放二级页表页号和对应的内存块号

缺页中断；

采用多级页表机制，各级页表的大小不能超过一个页面

两级页表的访存次数：如果没有快表共3次访存

n级页表访存n+1次

## 3.1.10 基本分段存储管理

地址空间按照逻辑关系划分为若干个段，每个段都有一个段名，每段从0开始编址

每个段是连续空间，但各个段可以不相邻

编程更方便，可读性更高

段号 + 段内地址

段号决定可以分几个段，段内地址决定每个段的最大长度是多少

一张映射表，段表

段表：段号，段长，基址

K号段对应的段表项存放的地址为M+K*段表项长度

**地址变换过程**

从PCB中进行段表寄存器的恢复

1. 根据逻辑地址得到段号、段内地址
2. 判断段号是否越界
3. 查询段表，找到对应的段表项
4. 检查段内地址是否超过段长
5. 计算得到物理地址
6. 访问目标内存单元

**两种方式的对比**

页是信息的物理单位：对用户不可见的，为了实现离散，提高内存利用率

段是信息的逻辑单位：分段对用户是可见的，能够更好地满足用户需求

分页的用户进程地址空间一维，分段是二维的

分段更容易实现共享和保护

## 3.1.11 段页式管理方式

先分段再分页

逻辑地址由段号，页号，页内偏移量组成

段表存放页表长度、页表存放块号

过程：

1. 根据逻辑地址得到段号、页号、页内偏移量
2. 判断越界
3. 查询段表得到页表块号（第一次访存）
4. 检查页号是否越界
5. 根据页表块号找到对应页表项（第二次访存）
6. 根据内存块号得到最后的物理地址
7. 访问目标内存单元（第三次访存）

## 3.2.1 虚拟地址的基本概念

一次性问题：大作业无法运行；多道程序并发度下降

驻留性问题：一个作业一直驻留在内存中

**虚拟内存**：根据动态调入数据

多次性：数据被多次调入内存

对换性：允许作业运行换入，换出

虚拟性：逻辑上扩充了内存容量

建立在离散分配的内存管理方式基础上

请求调页，页面置换功能

## 3.2.2 请求分页管理方式

访问的信息不在内存时，需要从外存调入内存

将内存暂时用不到的信息换出外存

**页表机制**

页表增加4个字段：状态位、访问字段、修改位、外存地址

**缺页中断机构**

当页面不在内存，产生缺页中断（内中断），直到调页完成后再唤醒

有空闲块则直接分配

没有空闲块需要一些算法

**地址变换机构**

新增请求调页和页面置换，修改请求页表的字段

## 3.2.3 页面置换算法

**OPT最佳置换算法**

每次选择淘汰的页面将是以后永不使用或者最长时间不再被访问的页面

缺页中断不一定发生页面置换

缺页率

前提是知道接下来访问的页面，无法实现

**FIFO先进先出算法**

每次淘汰的页面是最早进入内存的页面

Belady异常：进程分配的物理块增大，缺页次数不减反增的现象

算法性能差

**LRU最近最久未使用置换算法**

每次淘汰的页面是最近最久未使用的页面

性能好，实现困难，开销大

**时钟置换算法**

简单的时钟置换算法，设置访问位，将内存中的页面链接成一个循环队列，当被访问时其访问位置为1，当需要淘汰时，只坚持访问位，是0则换出，是1则置0，继续检查。

改进的时钟置换算法：只有被淘汰的页面被修改过时，才需要写回外存。

第一轮：查找（0，0），不修改

第二轮：查找（0，1），修改访问位为0

第三轮：查找（0，0），不修改

第四轮：查找（0，1）

## 3.2.4 页面分配策略、抖动、工作集

**驻留集**：请求分页存储管理中给进程分配的物理块的集合

固定分配：驻留集大小不变

可变分配：驻留集大小可变

局部置换：只能选进程自己的物理块进行置换

全局置换：可以将空闲物理块分配给缺页程序

**何时调入页面**：

预调页策略

请求调页策略

对换区

**抖动**：物理块数是不够的，刚换出的页面马上换入内存

工作集：某段时间间隔实际访问的集合

窗口尺寸

## 3.2.5 内存映射文件

内存映射文件：操作系统向上层提供的功能（系统调用

传统文件访问方式

mmap系统调用：将文件映射到进程的虚拟地址空间

实现文件的共享

# 文件系统

## 4.1.1 初识文件管理

**文件的属性**

名称：同一目录下不允许重名文件（为什么？

标识符：唯一，没有可读性

类型、路径、大小

**文件的组织**

无结构文件：二进制或字符流

有结构文件：记录、数据项

目录

**操作系统提供的功能**

create：创建文件

read：读文件

write：写文件，从内存写回外存

delete：删除文件

open & close；

**外存的管理**

文件的物理结构

存储空间的管理

**文件管理功能**：共享和保护

## 4.1.2 文件的逻辑结构

文件的逻辑结构

无结构文件：流式文件

有结构文件：记录式文件，关键字，定长记录和可变长记录

**顺序文件**：文件的记录顺序排列，物理上可以顺序或者链式存储

串结构：顺序与关键字无关

顺序结构：按关键字顺序排列

链式存储：无法实现随机存取

顺序存储：

1. 可变长记录，无法实现随机存取
2. 定长记录
   1. 可实现随机存取。记录长度为L，第i个记录存放的位置i*L
   2. 若采用串结构，无法快速找到某关键字对应的记录
   3. 若采用顺序结构，可以快速找到某关键字对应的记录（二分

**索引文件**

建立索引表，本身是定长记录的顺序文件

缺点：利用率低

**索引顺序文件**

一组记录对应一个索引表项

多级索引顺序文件

## 4.1.3 目录

**文件控制块FCB**

FCB的有序集合称为目录

FCB：目录文件的一条记录，包含文件名、物理地址、逻辑结构等基本信息

对目录的操作：搜索、创建、删除、显示、修改

**目录结构**

单级目录结构：整个系统只建立一张目录表，不允许文件重名，不适用于多用户操作系统

两级目录结构：分为主文件目录和用户文件目录

可以实现访问限制、允许不同用户的文件重名

多级目录结构（树形目录结构）：从根目录出发（“/”）的路径叫绝对路径，从当前目录出发（“.”）出发叫相对路径

不利于实现数据共享

无环图目录结构：指向同一个结点的有向无环图，设置共享计数器

**索引结点-对FCB的优化**

FCB只保留文件名和索引结点指针 

磁盘索引结点和内存索引结点 

## 4.1.4 文件的物理结构

对非空闲磁盘块的管理：文件物理结构和文件分配方式

磁盘中的存储单元被分为一个一个磁盘块/物理块

文件的逻辑地址空间也被分为一个一个的文件块

文件的逻辑地址 = 逻辑块号 + 块内地址

**连续分配**

每个文件在磁盘上占有一组连续的块

FCB中记录 起始块号和长度（总共占有几个块）

转换过程：物理块号 = 起始块号 + 逻辑块号

特点：支持顺序访问和直接访问；读写速度块；不方便文件的扩展；存储空间利用率低，会产生磁盘碎片

**链接分配**

-隐式链接：除文件的最后一个盘块之外，每个盘块都存有指向下一个盘块的指针

FCB中记录 起始块号和结束块号

特点：只支持顺序访问，i号逻辑块，总共需要 i + 1 次磁盘IO操作；方便文件的扩展，不会产生碎片；

-显式链接：用于链接文件的指针显式存放在文件分配表FAT中

FCB中记录 起始块号

特点：一张磁盘仅设置一张FAT，常驻内存

转换过程：逻辑块号 根据FAT 往后查询，逻辑块号转换成物理块号的过程不需要读磁盘

特点：支持随机访问；访问效率高；FAT需要占用一定的存储空间

**索引分配**

每个文件建立索引表，记录了文件逻辑块对应的物理块

FCB中存放顶级索引表的块号或者链接方案中的第一个索引表

索引表存放的磁盘块称为索引块，文件数据存放的磁盘称为数据块

转换过程：逻辑块号i，系统找到该文件的FCB，从FCB中找到索引表的存放位置并从外存读入内存，索引表起始地址 + i × 索引表项长度 得到物理块号

特点：支持随机访问；容易实现拓展；占用空间

*解决一个磁盘块存放不下索引的方法*

--链接方案：将多个索引块链接存放

特点：系统需要顺序读入每个索引块对应的磁盘块

--多层索引：使第一层索引块指向第二层索引块，以此类推

假如采用两层索引，文件的最大长度可以达到：256\*256\*1KB = 64 MB（磁盘块大小为1KB，一个表项占4B）

采用多层索引，每层索引表大小不能超过一个磁盘块

转换过程：访问1026号逻辑块，则 1026/256 = 4 ，1026%256 = 2，因此首先调入一级索引表，查询4号表项，将对应的二级索引表调入内存，再查询二级索引表的2号表项，得到逻辑块存放的磁盘块号，最后调入目标数据块（总共3次磁盘IO）

采用K层索引，访问一个数据块需要K + 1次

--混合索引：顶级索引表中，包含直接地址索引表，又包含一级间接索引表、二级间接索引表

假设顶级索引表没有读入内存：访问0~7号逻辑块需要两次读磁盘

优点：小文件读取更快，同时又能拥有很大的空间

考点

1. 根据多层索引，混合索引计算文件的最大长度（ps：各级索引表最大不能超过一个块）
2. 分析访问某个数据块所需要的读磁盘次数（ps：FCB中会存放指向顶级索引块的指针，可以根据FCB读入顶级索引块，每次读入下一级索引块都需要一次读磁盘操作，注意题目条件：顶级索引块是否已调入内存）

## 4.1.5 逻辑结构VS物理结构

逻辑结构：用户创建文件看到的视角，由用户自己决定文件内部的信息组织，操作系统并不关心

物理结构：操作系统决定文件采用什么方式存储，然后采用什么方式去将逻辑地址转变为物理地址，而这个过程用户并不关心。

## 4.1.6 文件存储空间管理

对空闲空间的管理

**存储空间的划分与初始化**

将物理磁盘划分为文件卷

物理磁盘分为目录区（存放FCB）和文件区（存放数据）

**管理方法**

--空闲表法：空闲块表（空闲盘块号，块数），适用于连续分配方式

可以采用首次适应、最佳适应、最坏适应等算法为文件分配连续的存储空间

--空闲链表法：适用于离散分配的物理结构

盘块链：以盘块为单位

盘区链：以盘区（一段连续的空闲盘块）为单位

回收：为一个文件分配多个盘块时效率高

--位示图法：每个二进制位对应一个盘块，常见“0”代表空闲，“1”代表已分配

用连续的“**字**”（字的字长决定转换公式）来表示，用（字号，位号）对应一个盘块号，类似一个二维数组

--成组链接法：超级块记录下一组空闲盘块数和空闲块号，每个分组的第一个盘块也需要记录下一组空闲盘块数和空闲快号

有上限，最后一组需要一定的标记

分配情况：检查第一个分组的块数是否足够，利用第一个磁盘块时需要把分组的信息复制给超级块

## 4.1.7 文件的基本操作

**创建文件**

create系统调用

1. 外存空间大小
2. 文件存放路径
3. 文件名

OS的操作：

1. 在外存中找到文件所需的空间
2. 找到该目录对应的目录文件，在目录中创建该文件对应的目录项

**删除文件**

delete系统调用

1. 文件存放路径
2. 文件名

OS的操作：

1. 找到文件名对应的目录项
2. 回收占用的磁盘块
3. 删除文件对应的目录项

**打开文件**

open系统调用

1. 文件存放路径
2. 文件名
3. 要对文件的操作类型

OS的操作：

1. 找到对应的目录项，检查操作权限
2. 将目录项添加到内存中的打开文件表中

两种打开文件表：

进程的打开文件表、系统的打开文件表（只有一张，设置一个打开计数器）

读写指针：指明该进程对文件的操作进行的位置

访问权限 

**关闭文件**

close系统调用

1. 将进程的打开文件表相应表项删除
2. 回收分配给文件的内存空间等资源
3. 系统打开文件表的打开计数器减一，如果为0，则删除对应表项

**读文件**

read系统调用完成读操作

1. 指明是哪一个文件、读入多少数据
2. 指明读入的数据存放在内存中的什么位置

OS的操作：

1. 从读指针指向的外存中，将用户指定大小的数据读入用户指定的内存区域中

**写文件**

write系统调用完成写操作

1. 指明是哪一个文件、写出多少数据
2. 指明写回内存的数据放在内存中的什么位置

OS的操作：

1. 从用户指定的内存区域，将指定大小的数据写回写指针指向的外存

## 4.1.8 文件共享

**基于索引结点的共享方式（硬链接**

目录项只包含文件名、索引结点指针

索引结点中设置一个链接计数变量

当计数变量为0时，系统负责删除文件

**基于符号链的共享方式（软链接**

Link类型的文件记录文件的存放路径，类似快捷方式

 采用软链接在删除文件时可能会出现失效的情况

## 4.1.9 文件保护

**口令保护**：空间开销小；存放在操作系统内部不安全

**加密保护**：最简单的加密算法：异或加密；时间长

**访问控制**：FCB中增加一个访问控制表ACL记录各个用户可以对该文件执行什么操作；

精简的访问列表：以‘组‘为单位，给用户分组

实现灵活

## 4.3.1 文件系统的层次结构

用户接口

文件目录系统

存取控制模块

逻辑文件系统与文件信息缓冲区

物理文件系统

辅助分配模块和设备管理模块

## 4.3.2 文件系统的全局布局

物理格式化：划分扇区，检测坏扇区，并用备用扇区替换坏扇区

逻辑格式化：磁盘分区，建立独立的文件系统

UNIX文件系统：引导块、超级块、空闲空间管理、i结点区、根目录

文件系统在内存中的结构：

内核区：

目录的缓存：近期访问的目录文件

系统打开文件表

进程打开文件表
