# 操作系统（陈向群）

## 第1讲 操作系统概述

### 1.1 操作系统做了什么？

暂无内容。

### 1.2 操作系统的定义及作用

#### 操作系统的三个作用

- 资源的管理者 → 有效
  - 自底向上

- 向用户提供各种服务 → 方便使用
  - 系统调用：进程的创建、执行；文件和目录的操作；I/O设备的使用；各类统计信息

- 对硬件机器的扩展 → 扩展能力
  - 硬件之上的第一层软件

### 1.3 操作系统的特征

#### 操作系统的主要特征

- 并发（concurrency）：指处理多个同时性活动的能力
  - 并行（parallel）：与并发相似，但多指不同程序同时在多个硬件部件上执行
- 共享（sharing）： 操作系统与多个用户的程序共同使用计算机系统中的资源（共享有限的系统资源）
  - 互斥共享、同时共享
- **虚拟**（virtual）：一个物理实体映射为若干个对应的逻辑实体—分时或分空间
  - 提高资源利用率
  - 虚处理机、虚拟地址空间、虚拟终端
- 随机： 操作系统必须随时对以不可预测的次序发生的事件进行响应并处理
  - 进程的运行速度不可预知、难以重现系统在某个时刻的状态

### 1.4 典型的操作系统架构

#### Windows架构

- 应用程序
- 系统功能调用
- 操作系统
- 计算机硬件

#### Android架构

- Android应用程序
- 应用程序框架
- 系统库和Android运行时
- Linux内核

### 1.5 操作系统分类

#### SPOOLING系统（技术）

- 同时的外围设备联机操作 ，又称假脱机技术（Simultaneous Peripheral Operation On-Line）
- 利用磁盘作缓冲，将输入、计算、输出分别组织成独立的任务流，使I/O和计算真正并行
- 现代计算机系统的打印过程通常采用的是SPOOLING技术

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\SPOOLING系统工作原理.png" alt="SPOOLING系统工作原理" style="zoom:30%;" />

#### 传统的操作系统分类

- 批处理操作系统 
- 分时系统 
  - 时间片（time slice）：操作系统将CPU的时间划分成若干个片段，称为时间片
  - 通用操作系统：分时优先，批处理在后
    - “前台”：需要频繁交互的作业
    - “后台”：时间性要求不强的作业
- 实时操作系统
  - 硬实时系统、软实时系统
- 个人计算机操作系统 
- 网络操作系统 
- 分布式操作系统 
  - 或以计算机网络为基础，或以多处理机为基础，基本特征是处理分布在不同计算机上
- 嵌入式操作系统

## 第2讲 操作系统运行环境与运行机制

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\第2讲.png" alt="第2讲" style="zoom:30%;" />

### 2.1 处理器状态（模式）

#### 控制和状态寄存器

- 用户可见寄存器
- 控制和状态寄存器
  - 程序计数器（PC：Program Counter），记录将要取出的指令的地址 
  - 指令寄存器（IR：Instruction Register），记录最近取出的指令 
  - 程序状态字（PSW：Program Status Word），记录处理器的运行状态如条件码、模式、控制位等信息

#### 处理器的状态（模式MODE）

- 内核态（Kernel Mode）：运行操作系统程序
  - 特权（privilege）指令：只能由操作系统使用、用户程序不能使用的指令
    - 启动I/O、内存清零、修改程序状态字、设置时钟、允许/禁止中断 、停机
- 用户态（User Mode）：运行用户程序
  - 非特权指令：用户程序可以使用的指令
    - 控制转移、算术运算、访管指令（由用户态陷入内核态）、取数指令

#### CPU状态之间的转换

- 用户态 → 内核态 
  - 唯一途径 → 中断/异常/陷入机制
  - 一条特殊的指令：陷入指令（又称访管指令：访问管理态supervisor mode）
    - 提供给用户程序的接口，用于调用操作系统的功能（服务） 
      - 例如：int，trap，syscall，sysenter/sysexit
- 内核态 → 用户态 
  - 设置程序状态字PSW

### 2.2 中断/异常机制

- 中断/异常对于操作系统的重要性
  - 就好比：汽车的发动机、飞机的引擎
  - 操作系统是由“中断驱动”或者“事件驱动”的

- CPU对系统发生的某个事件作出的一种反应
  - CPU暂停正在执行的程序，保留现场后自动转去执行相应事件的处理程序，处理完成后返回断点， 继续执行被打断的程序

- 中断的引入：为了支持CPU和设备之间的并行操作
  - 小明学习过程中接电话
- 异常的引入：表示CPU执行指令时本身出现的问题
  - 小明学习过程中喝水（口渴了）

#### 事件

- 中断（外中断）：外部事件，正在运行的程序所不期望的 
  - I/O中断 
    - ctrl+c、网络接收包、打印结束、读盘结束
  - 时钟中断
    - 定时器结束、时间片结束
  - 硬件故障
    - 电脑没电、读内存奇偶校验错
- 异常（内中断）：由正在执行的指令引发
  - 系统调用
  - 页故障/页错误 
    - 缺页异常
  - 保护性异常 
    - 写入只读区域、访问区域越界
  - 断点指令 
    - 单步调试
  - 其他程序性异常 （如算术溢出等）
    - 除0

#### 中断异常的小结

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\中断与异常的小结.png" alt="中断与异常的小结" style="zoom:30%;" />

### 2.3 中断/异常机制工作原理

硬件和软件相互配合而使计算机系统得以充分发挥能力。

- 硬件该做什么事？—中断/异常响应
  - 捕获中断源发出的中断/异常请求，以一定方式响应，将处理器控制权交给特定的处理程序
- 软件要做什么事？—中断/异常处理程序
  - 识别中断/异常类型并完成相应的处理

#### 中断响应

- 中断响应：发现中断、接收中断的过程
  - 由中断硬件部件完成

1. 设备发中断信号
2. 硬件保存现场（主要是程序计数器PC、程序状态字PSW）
3. 根据中断码查表
4. 把中断处理程序入口地址等推送到相应的寄存器
5. 执行中断处理程序

##### 中断向量表

- 中断向量：一个内存单元，存放中断处理程序入口地址和程序运行时所需的处理机状态字

#### 中断处理程序

- 设计操作系统时，为每一类中断/异常事件编好相应的处理程序，并设置好中断向量表

1. 保存相关寄存器信息 
2. 分析中断/异常的具体原因 
3. 执行对应的处理功能 
4. 恢复现场，返回被事件打断的程序

### 2.4 中断/异常机制实例

暂无内容。

### 2.5 系统调用机制

- 系统调用：用户在编程时可以调用的操作系统功能
  - 系统调用是操作系统提供给编程人员的唯一接口 
  - 使CPU状态从用户态陷入内核态
- 系统调用方式
  - 应用程序 → 系统调用
  - 应用程序 → C函数库/API接口 → 系统调用、

### 2.6 系统调用机制设计与执行过程

- 中断/异常机制
  - 支持系统调用服务的实现

- 选择一条特殊指令：陷入指令（亦称访管指令） 
  - 引发异常，完成用户态到内核态的切换 

- 系统调用号和参数 
  - 每个系统调用都事先给定一个编号（功能号）

- 系统调用表 
  - 存放系统调用服务例程的入口地址

#### 参数传递过程问题

常用的3种实现方法：

- 由陷入指令自带参数：陷入指令的长度有限，且还要携带系统调用功能号，只能自带有限的参数 
- （大多数情况）通过通用寄存器传递参数：这些寄存器是操作系统和用户程序都能访问的，但寄存器的个数会限制传递参数的数量 
- 在内存中开辟专用堆栈区来传递参数

#### 系统调用的执行过程

当CPU执行到特殊的陷入指令时：

1. 中断/异常机制：
   - 硬件保护现场；通过查中断向量表把控制权转给系统调用总入口程序 
2. 系统调用总入口程序：
   - 保存现场；将参数保存在内核堆栈里；
   - 通过查系统调用表把控制权转给相应的系统调用处理例程或内核函数 
3. 执行系统调用例程 
4. 恢复现场，返回用户程序

### 2.7 例子：Linux系统调用实现

暂无内容。

## 第3讲 进程/线程模型

### 3.1 进程基本概念

#### 多道程序设计

- 允许多个程序同时进入内存并运行，其目的是为了提高系统效率

#### 并发环境与并发程序

- 并发环境：一段时间间隔内，单处理器上有两个或两个以上的程序同时处于开始运行但尚未结束的状态， 并且次序不是事先确定的
- 并发程序：在并发环境中执行的程序 

#### 进程的定义

- 进程（对CPU的抽象）是具有独立功能的程序关于某个数据集合上的一次运行活动，是系统进行资源分配和调度的独立单位
  - 程序的一次执行过程 
    - 同一程序运行两次，算两个进程
  - 是正在运行程序的抽象 
  - 将一个CPU变幻成多个虚拟的CPU 
  - 系统资源以进程为单位分配，如内存、文件、……
    -  每个具有独立的地址空间 
  - 操作系统将CPU调度给需要的进程

#### 进程控制块PCB

- PCB：Process Control Block 
  - 又称 进程描述符、进程属性 
  - 操作系统用于管理控制进程的一个专门数据结构 
  - 记录进程的各种属性，描述进程的动态变化过程
- PCB是系统感知进程存在的唯一标志
  - 进程与PCB是一一对应的
- 进程表：所有进程的PCB集合

##### PCB的内容

- 进程描述信息
  - 进程标识符（process ID），唯一，通常是一个整数 
  - 进程名，通常基于可执行文件名， 不唯一 
  - 用户标识符（user ID） 
  - 进程组关系
- 进程控制信息
  - 当前状态
  - 优先级（priority） 
  - 代码执行入口地址 
  - 程序的磁盘地址 
  - 运行统计信息（执行时间、页面调度） 
  - 进程间同步和通信 
  - 进程的队列指针 
  - 进程的消息队列指针
- 所拥有的资源和使用情况
  - 虚拟地址空间的状况等
  - 打开文件列表
- CPU现场信息
  - 寄存器值（通用寄存器、程序计数器PC、程序状态字PSW、栈指针）
  - 指向该进程页表的指针

### 3.2 进程状态及状态转换

#### 进程的三种基本状态

进程的三种基本状态： 

- 运行态（Running）
  - 占有CPU，并在CPU上运行
- 就绪态（Ready）
  - 已经具备运行条件，但由于没有空闲CPU，而暂时不能运行
- 等待态（Waiting/Blocked）：也称阻塞态、封锁态、睡眠态
  - 因等待某一事件而暂时不能运行
    - 如等待读盘结果

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\三状态模型及状态转换.png" alt="三状态模型及状态转换" style="zoom:30%;" />

#### 进程的其他状态

- 创建（New）
  - 已完成创建一进程所必要的工作
    - PID、PCB
  - 但尚未同意执行该进程
    - 因为资源有限
- 终止（Terminated）
  - 终止执行后，进程进入该状态
  - 可完成一些数据统计工作
  - 资源回收
- 挂起（Suspend）
  - 用于调节负载  
  - 进程不占用内存空间，其进程映像交换到磁盘上

#### 进程队列

- 操作系统为每一类进程建立一个或多个队列 
  - 多个等待队列等待的事件不同 
  - 就绪队列也可以多个 
  - 单CPU情况下，运行队列中只有一个进程
- 队列元素为PCB 
- 伴随进程状态的改变，其PCB从一个队列进入另一个队列

### 3.3 进程控制

进程控制操作完成进程各状态之间的转换，由具有特定功能的原语完成 

- 进程创建原语 
- 进程撤消原语 
- 阻塞原语 
- 唤醒原语 
- 挂起原语 
- 激活原语 
- 改变进程优先级

##### 原语（primitive）

- 完成某种特定功能的一段程序，具有不可分割性或不可中断性 
  - 即原语的执行必须是连续的，在执行过程中不允许被中断 
  - 原子操作（atomic）

#### 进程的创建

- 给新进程分配一个唯一标识以及进程控制块 
- 为进程分配地址空间 
- 初始化进程控制块 
  - 设置默认值（如: 状态为 New，...） 
- 设置相应的队列指针 
  - 如: 把新进程加到就绪队列链表中
- UNIX：fork/exec
- WINDOWS：CreateProcess

#### 进程的撤销

- 结束进程 
- 收回进程所占有的资源 
  - 关闭打开的文件、断开网络连接、回收分配的内存、…… 
- 撤消该进程的PCB
- UNIX：exit
- WINDOWS：TerminateProcess

#### 进程阻塞

- 处于运行状态的进程，在其运行过程中期待某一事件发生
  - 如等待键盘输入、等待磁盘数据传输完成、等待其它进程发送消息
- 当被等待的事件未发生时，由进程自己执行阻塞原语，使自己由运行态变为阻塞态
- UNIX：wait
- WINDOWS：WaitForSingleObject

#### UNIX几个进程控制操作（系统调用）

- fork（） 通过复制调用进程来建立新的进程，是最基本的进程建立过程 
- exec（） 包括一系列系统调用，它们都是通过用一段新的程序代码覆盖原来的地址空间，实现进程执行代码的转换 
- wait（） 提供初级进程同步操作，能使一个进程等待另外一个进程的结束 
- exit（） 用来终止一个进程的运行

### 3.4 深入理解进程概念

##### 进程的分类

- 系统进程 VS 用户进程
- 前台进程 VS 后台进程
- CPU密集型进程 VS I/O密集型进程

##### 进程层次结构

- UNIX进程家族树：init为根
- Windows：地位相同

#### 进程与程序的区别

- 进程更能准确刻画并发，而程序不能 
- 程序是静态的，进程是动态的 
- 进程有生命周期的，有诞生有消亡，是短暂的
  - 而程序是相对长久的 
- 一个程序可对应多个进程 
- 进程具有创建其他进程的功能
- 操作系统给每个进程都分配了一个地址空间

#### 进程映像

对进程执行活动全过程的静态描述。

- 用户相关：进程地址空间（包括代码段、数据段、 堆和栈、共享库……）
- 寄存器相关：程序计数器、指令寄存器、程序状态 寄存器、栈指针、通用寄存器等的值
- 内核相关： 
  - 静态部分：PCB及各种资源数据结构 
  - 动态部分：内核栈（不同进程在进入内核后使用 不同的内核栈）

#### 上下文切换

将CPU硬件状态从一个进程换到另一个进程的过程称为上下文切换。

- 进程运行时，其硬件状态保存在CPU上的寄存器中 
  - 寄存器：程序计数器、程序状态寄存器、栈指针、 通用寄存器、其他控制寄存器的值
- 进程不运行时，这些寄存器的值保存在进程控制块PCB中
  - 当操作系统要运行一个新的进程时，将 PCB中的相关值送到对应的寄存器中

### 3.5 线程的引入

为什么在进程中再派生线程？

- 应用的需要 
  - 进程并发无法共享内存空间
- 开销的考虑 
  - 进程相关的操作时间/空间开销大， 限制了并发度的提高
  - 线程的开销小
    - 创建一个新线程花费时间少（撤销亦如此） 
    - 两个线程切换花费时间少
    - 线程之间相互通信无须调用内核（同一进程内的线程共享内存和文件）
- 性能的考虑
  - 多个线程，有的计算，有的I/O

#### 线程的基本概念

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\线程的基本概念.png" alt="线程的基本概念" style="zoom:30%;" />

- 线程：进程中的一个运行实体，是CPU的调度单位
  - 有时将线程称为轻量级进程
- 有标示符ID 
- 有状态及状态转换 → 需要提供一些操作 
- 不运行时需要保存的上下文 
  - 有上下文环境：程序计数器等寄存器 
- 有自己的栈和栈指针 √ 
- 共享所在进程的地址空间和其他资源 
- 可以创建、撤消另一个线程
  - 程序开始是以一个单线程进程方式运行的

### 3.6 线程机制的实现

- 用户级线程（UNIX）
  - 在用户空间建立线程库： 提供一组管理线程的过程 
  - 运行时系统：完成线程的管理工作（操作、线程表） 
  - 内核管理的还是进程，不知道线程的存在 
  - 线程切换不需要内核态特权
- 核心级线程（Windows）
  - 内核管理所有线程管理， 并向应用程序提供API 接口 
  - 内核维护进程和线程的上下文 
  - 线程的切换需要内核支持 
  - 以线程为基础进行调度
- 混合—两者结合方法（Solaris）
  - 线程创建在用户空间完成 
  - 线程调度等在核心态完成

## 第4讲 处理器调度（CPU调度）

### 4.1 CPU调度的相关概念

- CPU调度的任务是控制、协调进程对CPU的竞争 
  - 即按一定的调度算法从就绪队列中选择一个进程， 把CPU的使用权交给被选中的进程 
    - 如果没有就绪进程，系统会安排一个系统空闲进程或idle进程

#### CPU调度要解决的三个问题

- WHAT：按什么原则选择下一个要执行的进程 
  - 调度算法 
- WHEN：何时选择 
  - 调度时机 
- HOW： 如何让被选中的进程上CPU运行 
  - 调度过程（进程的上下文切换）

#### CPU调度的时机（进程状态转化时）

- 进程正常终止或由于某种错误而终止 
- 新进程创建或一个等待进程变成就绪 
- 当一个进程从运行态进入阻塞态 
- 当一个进程从运行态变为就绪态

#### 调度过程——进程切换

- 进程切换：是指一个进程让出处理器，由另一个进程占用处理器的过程
  - 切换全局页目录以加载一个新的地址空间 
  - 切换内核栈和硬件上下文，其中硬件上下文包括了内核执行新进程需要的全部信息，如CPU相关寄存器
- 切换过程包括了对原来运行进程各种状态的保存和对新的进程各种状态的恢复 
- 上下文切换开销（COST）
  - 直接开销：内核完成切换所用的CPU时间 
    - 保存和恢复寄存器…… 
    - 切换地址空间（相关指令比较昂贵） 
  - 间接开销 
    - 高速缓存(Cache)、缓冲区缓存(Buffer Cache)和 TLB(Translation Look-aside Buffer)失效

#### CPU调度算法的设计

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\CPU调度算法的设计.png" alt="CPU调度算法的设计" style="zoom:30%;" />

- 调度算法衡量指标
  - 吞吐量 Throughput 
    - 每单位时间完成的进程数目 
  - 周转时间TT(Turnaround Time) 
    - 每个进程从提出请求到运行完成的时间 
  - 响应时间RT(Response Time) 
    - 从提出请求到第一次回应的时间 
  - 其他 
    - CPU 利用率(CPU Utilization) 
      - CPU做有效工作的时间比例 
    - 等待时间(Waiting time) 
      - 每个进程在就绪队列(ready queue)中等待的时间

### 4.2 设计调度算法前的要点讨论

- 进程优先级
  - 静态优先级：进程创建时指定，运行过程中不再改变 
  - 动态优先级： 进程创建时指定了一个优先级，运行过程中可以动态变化 
    - 如：等待时间较长的进程可提升其优先级 
- 抢占与非抢占
  - 可抢占式Preemptive（可剥夺式） 
    - 当有比正在运行的进程优先级更高的进程就绪时，系统可强行剥夺正在运行进程的CPU，提供给具有更高优先级的进程使用
  - 不可抢占式Non-preemptive（不可剥夺式 ）
    - 某一进程被调度运行后，除非由于它自身的原因不能运行，否则一直运行下去

- I/O密集型与CPU密集型
  - I/O密集型或I/O型(I/O-bound) 
    - 频繁的进行I/O，通常会花费很多时间等待I/O操作的完成 
  - CPU密集型或CPU型或计算密集型(CPU-bound) 
    - 需要大量的CPU时间进行计算
- 时间片
  - Time slice 或 quantum 
  - 一个时间段，分配给调度上CPU的进程，确定了允许该进程运行的时间长度
  - 考虑因素：
    - 进程切换的开销 
    - 对响应时间的要求 
    - 就绪进程个数 
    - CPU能力 
    - 进程的行为

### 4.3 批处理系统的调度算法

#### 先来先服务（FCFS - First Come First Serve）

- 按照进程就绪的先后顺序使用CPU
- 非抢占
- 优缺点 
  - 公平 
  - 实现简单 
  - 长进程后面的短进程需要等很长时间，不利于用户体验

#### 最短作业优先（SJF - Shortest Job First）

- 具有最短完成时间的进程优先执行
- 非抢占式
- 优缺点 
  - 最短的平均周转时间
    - 在所有进程同时可运行时， 采用SJF调度算法可以得到最短的平均周转时间
  - 不公平
    - 源源不断的短任务到来，可能使长的任务长时间得不到运行 → 产生 “饥饿”现象 (starvation)

#### 最短剩余时间优先（SRTN - Shortest Remaining Time Next）

- 当一个新就绪的进程比当前运行进程具有更短的完成时间时，系统抢占当前进程， 选择新就绪的进程执行

- SJF抢占式版本

#### 最高响应比优先 （HRRN - Highest Response Ratio Next）

- 是一个综合的算法 
  - 调度时，首先计算每个进程的响应比R；之后， 总是选择 R 最高的进程执行

$$
响应比R = 周转时间 / 处理时间 =（处理时间 + 等待时间）/ 处理时间 = 1 +（等待时间 / 处理时间）
$$

### 4.4 交互式系统的调度算法

#### 轮转调度（RR - Round Robin）

- 目标 
  - 为短任务改善平均响应时间 
- 解决问题的思路 
  - 周期性切换 
  - 每个进程分配一个时间片
  - 时钟中断 → 轮换
- 如何选择合适的时间片?
  - 太长 --大于典型的交互时间 
    - 降级为先来先服务算法 
    - 延长短进程的响应时间 
  - 太短 --小于典型的交互时间 
    - 进程切换浪费CPU时间
- 优缺点 
  - 公平 
  - 有利于交互式计算，响应时间快 
  - 由于进程切换，时间片轮转算法要花费较高的开销
  - RR对不同大小的进程是有利的 
    - 但是对于相同大小的进程呢？
      - 不如FCFS

##### 虚拟轮转法（Virtual RR）

- 利用辅助队列来“照顾”I/O密集型进程

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\虚拟轮转法（Virtual RR）.png" alt="虚拟轮转法（Virtual RR）" style="zoom:30%;" />

#### 最高优先级调度（HPF - Highest Priority First）

- 选择优先级最高的进程投入运行
  - 系统进程优先级高于用户进程 
  - 前台进程优先级高于后台进程 
  - 操作系统更偏好 I/O型进程
- 优先级可以是静态不变的，也可以动态调整 
  - 优先数可以决定优先级
- 就绪队列可以按照优先级组织 
- 实现简单；不公平

##### 优先级反转问题

- Priority Inversion
- 又称：优先级反置、翻转、倒挂
- 一个低优先级进程持有一个高优先级进程所需要的资源，使得高优先级进程等待低优先级进程运行
- 影响 
  - 系统错误 
  - 高优先级进程停滞不前，导致系统性能降低 
- 解决方案 
  - 设置优先级上限（进入临界区的上限）
  - 优先级继承
  - 使用中断禁止
  

### 4.5 多级反馈队列调度算法、各种调度算法比较、多处理器调度算法

#### 多级反馈队列（Multiple feedback queue）

- 设置多个就绪队列，第一级队列优先级最高 
- 给不同就绪队列中的进程分配长度不同的时间片，第一级队列时间片最小；随着队列优先级别的降低，时间片增大 
- 当第一级队列为空时，在第二级队列调度，以此类推 
- 各级队列按照时间片轮转方式进行调度 
- 当一个新创建进程就绪后，进入第一级队列 
- 进程用完时间片而放弃CPU，进入下一级就绪队列 
- 由于阻塞而放弃CPU的进程进入相应的等待队列，一旦等待的事件发生，该进程回到原来一级就绪队列
- 非抢占式

#### 最短进程优先（Shortest Process Next）

- 同SJF

#### 小结：各种调度算法的比较

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\小结：各种调度算法的比较.png" alt="小结：各种调度算法的比较" style="zoom:30%;" />

### 4.6 多处理器调度算法需要考虑的问题

- 不仅要决定选择哪一个进程执行 
  - 还需要决定在哪一个CPU上执行
- 要考虑进程在多个CPU之间迁移时的开销 
  - 高速缓存失效、TLB失效 
  - 尽可能使进程总是在同一个CPU上执行 
    - 如果每个进程可以调度到所有CPU上，假如进程上次在CPU1上执行，本次被调度到CPU2，则会增加 高速缓存失效、TLB失效；如果每个进程尽量调度到指定的CPU上，各种失效就会减少 
- 考虑负载均衡问题

### 4.7 典型操作系统的调度算法

- UNIX 
  - 动态优先数法 
- 5.3BSD 
  - 多级反馈队列法 
- Linux 
  - 抢占式调度 
- Windows 
  - 基于优先级的抢占式多任务调度 
- Solaris 
  - 综合调度算法

### 4.6 WINDOWS线程调度

- 调度单位是线程 
- 采用基于动态优先级的、抢占式调度，结合时间配额的调整 
- 就绪线程按优先级进入相应队列 
- 系统总是选择优先级最高的就绪线程运行 
- 同一优先级的各线程按时间片轮转进行调度 
- 多CPU系统中允许多个线程并行运行
- 引发线程调度的条件（除进程状态变化之外的条件）： 
  - 一个线程的优先级改变了 
  - 一个线程改变了它的亲和(Affinity)处理机集合

## 第5讲 同步互斥机制1

### 5.1 进程并发执行

- 并发是所有问题产生的基础
- 并发是操作系统设计的基础

#### 从进程的特征出发

- 并发 
  - 进程的执行是间断性的 
  - 进程的相对执行速度不可预测
- 共享 
  - 进程/线程之间的制约性
- 不确定性（源于随机性）
  - 进程执行的结果与其执行的相对速度有关，是不确定的

### 5.2 进程互斥

#### 进程互斥

- 由于各进程要求使用共享资源（变量、文件等）， 而这些资源需要排他性使用各进程之间竞争使用这些资源 
  - 这一关系称为进程互斥
- 临界资源：critical resource 
  - 系统中某些资源一次只允许一个进程使用，称这样的资源为临界资源或互斥资源或共享变量
- 临界区(互斥区)：critical section(region) 
  - 各个进程中对某个临界资源（共享变量）实施操作的程序片段

#### 临界区（互斥区）的使用原则

- 没有进程在临界区时，想进入临界区的进程可进入 
- 不允许两个进程同时处于其临界区中 
- 临界区外运行的进程不得阻塞其他进程进入临界区 
- 不得使进程无限期等待进入临界区

#### 实现进程互斥的方案

- 软件方案 
  - Dekker解法、Peterson解法
- 硬件方案
  - 屏蔽中断、TSL(XCHG)指令

### 5.3 进程互斥的软件解决方案

暂无内容。

### 5.4 进程互斥的硬件解决方案

#### 中断屏蔽方法

- “开关中断”指令
  - 执行“关中断”指令 
  - 临界区操作 
  - 执行“开中断”指令
- 简单，高效 
- 代价高，限制CPU并发能力（临界区大小）
- 不适用于多处理器 
- 适用于操作系统本身，不适于用户进程

#### 小结

- 软件方法 
  - 编程技巧 
- 硬件方法 
- 忙等待(busy waiting) 
  - 进程在得到临界区访问权之前，持续测试而不做其他事情 
  - 自旋锁 Spin lock (多处理器 √) 
- 优先级反转（倒置）

### 5.5 进程同步

- 进程同步：synchronization 
  - 指系统中多个进程中发生的事件存在某种时序关系，需要相互合作，共同完成一项任务

#### 生产者/消费者问题：又称为有界缓冲区问题 

问题描述： 

- 一个或多个生产者生产某种类型的数据放置在缓冲区中 
- 有消费者从缓冲区中取数据，每次取一项 
- 只能有一个生产者或消费者对缓冲区进行操作

要解决的问题：

- 当缓冲区已满时， 生产者不会继续向其中添加数据
- 当缓冲区为空时， 消费者不会从中移走数据 

### 5.6 信号量及P、V操作

- 一个特殊变量 

- 用于进程间传递信息的一个整数值 

- 定义如下： 

```c
  struc semaphore { 
      int count; 
      queueType queue; 
  } 
```

- 信号量说明：`semaphore s; `
- 对信号量可以实施的操作：初始化、P和V（P、V分别 是荷兰语的test(proberen)和increment(verhogen)）
  - P：down, semWait 
  - V：up, semSignal
- P、V操作为原语操作(primitive or atomic action)
- 在信号量上定义了三个操作 
  - 初始化(非负数)、P操作、V操作
- 最初提出的是二元信号量（解决互斥）之后，推广到一般信号量（多值）或计数信号量 （解决同步）

#### 用P、V操作解决进程间互斥问题

- 分析并发进程的关键活动，划定临界区 
- 设置信号量 mutex，初值为1 
- 在临界区前实施 P(mutex) 
- 在临界区之后实施 V(mutex)

### 5.7 生产者消费者问题

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\用信号量解决生产者消费者问题.png" alt="用信号量解决生产者消费者问题" style="zoom:30%;" />

### 5.8 读者写者问题

问题描述： 

- 多个进程共享一个数据区，这些进程分为两组： 

  - 读者进程：只读数据区中的数据 

  - 写者进程：只往数据区写数据 

要求满足条件： 

- 允许多个读者同时执行读操作 
- 不允许多个写者同时操作 
- 不允许读者、写者同时操作

#### 第一类读者写者问题：读者优先

如果读者执行： 

- 无其他读者、写者，该读者可以读 
- 若已有写者等，但有其他读者正在读，则该读者也可以读 
- 若有写者正在写，该读者必须等 

如果写者执行： 

- 无其他读者、写者，该写者可以写 
- 若有读者正在读，该写者等待 
- 若有其他写者正在写，该写者等待 

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\第一类读者写者问题的解法.png" alt="第一类读者写者问题的解法" style="zoom:30%;" />

## 第6讲 同步互斥机制2、进程通信机制

### 6.1 管程 MONITOR

#### 为什么会出现管程？

问题：

- 信号量机制的不足：程序编写困难、易出错

方案：

- 在程序设计语言中引入管程成分方案 
- 一种高级同步机制 

#### 管程的定义

- 是一个特殊的模块 
- 有一个名字 
- 由关于共享资源的数据结构及在其上操作的一组过程组成
- 进程与管程 
  - 进程只能通过调用管程中的过程来间接地访问管程中的数据结构 

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\管程的定义.png" alt="管程的定义" style="zoom:30%;" />

#### 管程要保证什么？

- 作为一种同步机制，管程要解决两个问题 
  - 互斥 
    - 管程是互斥进入的 
      - 为了保证管程中数据结构的数据完整性 
    - 管程的互斥性是由编译器负责保证的 
  - 同步
    - 管程中设置条件变量及等待/唤醒操作以解决同步问题 
    - 可以让一个进程或线程在条件变量上等待（此时，应先释放管程的使用权），也可以通过发送信号将等待在条件变量上的进程或线程唤醒

### 6.2 HOARE管程

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\HOARE管程示意图.png" alt="HOARE管程示意图" style="zoom:30%;" />

- 因为管程是互斥进入的，所以当一个进程试图进入一个已被占用的管程时，应当在管程的入口处等待 
  - 为此，管程的入口处设置一个进程等待队列，称作入口等待队列 
- 如果进程P唤醒进程Q，则P等待Q执行；如果进程Q 执行中又唤醒进程R，则Q等待R执行；……，如此， 在管程内部可能会出现多个等待进程 
  - 在管程内需要设置一个进程等待队列，称为紧急等待队列，紧急等待队列的优先级高于入口等待队列的优先级

#### HOARE管程——条件变量的实现

- 条件变量——在管程内部说明和使用的一种特殊类型的变量 
- `var c:condition;` 
- 对于条件变量，可以执行wait和signal操作
  - wait(c)：如果紧急等待队列非空，则唤醒第一个等待者；否则释放管程的互斥权，执行此操作的进程进入c链末尾
  - signal(c)：如果c链为空，则相当于空操作，执行此操作的进程继续执行；否则唤醒第一个等待者，执行此操作的进程进入紧急等待队列的末尾

### 6.3 管程的应用

- 管程实现的两个主要途径： 
  - 直接构造 → 效率高 
  - 间接构造 → 用某种已经实现的同步机制去构造
    - 例如：用信号量及P、 V操作构造管程

### 6.4 MESA管程

- Hoare管程的一个缺点 
  - 两次额外的进程切换
- 解决： 
  - signal → notify 
  - notify：当一个正在管程中的进程执行notify(x) 时，它使得x条件队列得到通知，发信号的进程继续执行

#### 使用NOTIFY要注意的问题

- notify的结果：位于条件队列头的进程在将来合适的时候且当处理器可用时恢复执行
- 由于不能保证在它之前没有其他进程进入管程， 因而这个进程必须重新检查条件 → 用while循环取代if语句
- 导致对条件变量至少多一次额外的检测（但不再有额外的进程切换），并且对等待进程在notify 之后何时运行没有任何限制

#### 改进NOTIFY

- 对notify的一个很有用的改进
  - 给每个条件原语关联一个监视计时器，不论是否被通知，一个等待时间超时的进程将被设为就绪态 
  - 当该进程被调度执行时，会再次检查相关条件， 如果条件满足则继续执行 
- 超时可以防止如下情况的发生： 
  - 当某些进程在产生相关条件的信号之前失败时， 等待该条件的进程就会被无限制地推迟执行而处于饥饿状态

#### 引入BROADCAST

- broadcast：使所有在该条件上等待的进程都被释放并进入就绪队列 
- 当一个进程不知道有多少进程将被激活时，这种方式是非常方便的 
  - 例子：生产者/消费者问题中，假设insert和remove 函数都适用于可变长度的字符块，此时，如果一个生产者往缓冲区中添加了一批字符，它不需要知道每个正在等待的消费者准备消耗多少字符，而仅仅执行一个broadcast，所有正在等待的进程都得到通知并再次尝试运行 
- 当一个进程难以准确判定将激活哪个进程时，也可使用广播

#### HOARE管程与MESA管程的比较

- Mesa管程优于Hoare管程之处在于Mesa管程错误比较少 
- 在Mesa管程中，由于每个过程在收到信号后都重新检查管程变量，并且由于使用了while结构， 一个进程不正确的broadcast广播或发信号notify， 不会导致收到信号的程序出错
- 收到信号的程序将检查相关的变量，如果期望的条件没有满足，它会重新继续等待

#### 管程小结

- 管程：抽象数据类型 
  - 有一个明确定义的操作集合，通过它且只有通过它才能操纵该数据类型的实例 
- 实现管程结构必须保证下面几点： 
  - 只能通过管程的某个过程才能访问资源； 
  - 管程是互斥的，某个时刻只能有一个进程或线程调用管程中的过程 
- 条件变量：为提供进程与其他进程通信或同步而引入 
  - wait/signal 或 wait/notify 或 wait/broadcast 

### 6.5 PTHREAD中同步机制

暂无内容。

### 6.6 进程间通信 IPC

- 消息传递：消息队列
- 共享内存 
- 管道 
  - 利用一个缓冲传输介质——内存或文件连接两个相互通信的进程
    - 字符流方式写入读出 
    - 先进先出顺序 
    - 管道通信机制必须提供的协调能力 
    - 互斥、同步、判断对方进程是否存在
- 套接字 
- 远程过程调用

### 6.7 典型操作系统中的IPC机制

- UNIX
  - 管道、消息队列、共享内存、信号量、信号、 套接字
- Linux
  - 管道、消息队列、共享内存、信号量、信号、 套接字 
  - 内核同步机制：原子操作、自旋锁、读写锁、 信号量、屏障、BKL
- Windows
  - 同步对象：互斥对象、事 件对象、信号量对象、临界区对象、互锁变量 
  - 套接字、文件映射、管道、 命名管道、邮件槽、剪贴板、动态数据交换、对象连接与嵌入、动态链接库、 远程过程调用

## 第7讲 存储模型1

### 7.1 基本概念：地址重定位 RELOCATION

- 地址转换、地址映射、地址翻译

#### 已经了解的

- 程序装载到内存才可以运行
  - 通常，程序以可执行文件格式保存在磁盘上 
- 多道程序设计模型 
  - 允许多个程序同时进入内存 
- 每个进程有自己的地址空间 
  - 一个进程执行时不能访问另一个进程的地址空间 
  - 进程不能执行不适合的操作

#### 复习：进程地址空间

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\进程地址空间.png" alt="进程地址空间" style="zoom:30%;" />

#### 讨论

- 进程中的地址不是最终的物理地址 
- 在进程运行前无法计算出物理地址 
  - 因为：不能确定进程被加载到内存什么地方 
  - 需要地址重定位的支持 
    - 地址转换、地址变换、地址翻译、地址映射 Translation、Mapping

#### 地址重定位

为了保证CPU执行指令时可正确访问内存单元，需要将用户程序中的逻辑地址转换为运行时可由机器直接寻址的物理地址，这一过程称为地址重定位。

- 逻辑地址（相对地址，虚拟地址） 
  - 用户程序经过编译、汇编后形成目标代码，目标代码通常采用相对地址的形式，其首地址为0， 其余地址都相对于首地址而编址 
    - 不能用逻辑地址在内存中读取信息 
- 物理地址（绝对地址，实地址） 
  - 内存中存储单元的地址 
    - 可直接寻址

#### 静态重定位与动态重定位

- 静态重定位：当用户程序加载到内存时，一次性实现逻辑地址到物理地址的转换 
  - 一般可以由软件完成 
- 动态重定位：在进程执行过程中进行地址变换 → → 即逐条指令执行时完成地址转换 
  - 需要硬件部件支持

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\动态重定位实现.png" alt="动态重定位实现" style="zoom:30%;" />

### 7.2 物理内存管理

#### 空闲内存管理

- 位图 
  - 每个分配单元对应于位图中的一位，0表示空闲，1 表示占用（或者相反） 
- 空闲区表、已分配区表 
  - 表中每一项记录了空闲区（或已分配区）的起始地址、长度、标志 
- 空闲块链表 

- 等长划分 VS 不等长划分

#### 内存分配算法

将该空闲区分为两部分，一部分供进程使用，另一部分形成新的空闲区 。

- 首次适配 first fit 
  - 在空闲区表中找到第一个满足进程要求的空闲区 
- 下次适配 next fit 
  - 从上次找到的空闲区处接着查找 
- 最佳适配 best fit 
  - 查找整个空闲区表，找到能够满足进程要求的最小空闲区 
- 最差适配 worst fit 
  - 总是分配满足进程要求的最大空闲区

#### 回收问题

- 内存回收算法 
  - 当某一块归还后，前后空闲空间合并，修改内存空闲区表 
  - 四种情况
    - 上相邻、下相邻、上下都相邻、上下都不相邻

### 7.3 伙伴系统 BUDDY SYSTEM

- 一种经典的内存分配方案 
- 主要思想：将内存按2的幂进行划分，组成若干空闲块链表；查找该链表找到能满足进程需求的最佳匹配块 
- 算法： 
  - 首先将整个可用空间看作一块： 2^U 
  - 假设进程申请的空间大小为 s，如果满足 2^U-1 < s <= 2^U，则分配整个块 
    - 否则，将块划分为两个大小相等的伙伴，大小为2^U-1 
  - 一直划分下去直到产生大于或等于 s 的最小块

### 7.4 基本内存管理方案1：整个进程进入内存中一片连续区域

#### 基本内存管理方案

- 单一连续区 
- 固定分区 
- 可变分区 
- 页式
- 段式 
- 段页式 

#### 单一连续区

- 特点：一段时间内只有一个进程在内存 
  - 简单，内存利用率低

#### 固定分区

- 把内存空间分割成若干区域，称为分区 
- 每个分区的大小可以相同也可以不同 
- 分区大小固定不变 
- 每个分区装一个且只能装一个进程

#### 可变分区 

- 根据进程的需要，把内存空闲空间分割出一个分区，分配给该进程 
- 剩余部分成为新的空闲区

##### 碎片问题解决：外部碎片

- 碎片 → 很小的、不易利用的空闲区 
  - 导致内存利用率下降 
- 解决方案 → 紧缩技术（memory compaction） 
  - 在内存移动程序，将所有小的空闲区合并为较大的空闲区 
  - 又称：压缩技术，紧致技术，搬家技术 
- 紧缩时要考虑的问题 
  - 系统开销 、移动时机 

### 7.5 基本内存管理方案2：一个进程进入内存中若干片不连续的区域

#### 页式存储管理方案

- 设计思想 
  - 用户进程地址空间被划分为大小相等的部分，称为页（page）或页面，从0开始编号 
  - 内存空间按同样大小划分为大小相等的区域，称为页框（page frame），从0开始编号；也称为物理页面，页帧，内存块 
- 内存分配（规则） 
  - 以页为单位进行分配，并按进程需要的页数来分配；逻辑上相邻的页，物理上不一定相邻 
- 典型页面尺寸：4K 或 4M

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\页式存储管理方案.png" alt="页式存储管理方案" style="zoom:30%;" />

##### 相关数据结构及地址转换

- 页表 
  - 页表项：记录了逻辑页号与页框号的对应关系 
  - 每个进程一个页表，存放在内存 
  - 页表起始地址保存在何处？ （寄存器、PCB）
- 空闲内存管理 
- 地址转换（硬件支持） 
  - CPU取到逻辑地址，自动划分为页号和页内地址； 用页号查页表，得到页框号，再与页内偏移拼接成为物理地址

#### 段式存储管理方案

- 设计思想 
  - 用户进程地址空间：按程序自身的逻辑关系划分为若干个程序段，每个程序段都有一个段名 
  - 内存空间被动态划分为若干长度不相同的区域，称为物理段，每个物理段由起始地址和长度确定 
  - 内存分配（规则）：以段为单位进行分配，每段在内存中占据连续空间，但各段之间可以不相邻

##### 相关数据结构及地址转换

- 段表 
  - 每项记录了段号、段首地址和段长度之间的关系 
  - 每个进程一个段表，存放在内存 
  - 段表起始地址保存在何处？ 
- 物理内存管理 
- 地址转换（硬件） 
  - CPU取到逻辑地址，用段号查段表，得到该段在内存的起始地址，与段内偏移地址计算出物理地址

#### 段页式存储管理方案

- 产生背景 
  - 综合页式、段式方案的优点，克服二者的缺点 
- 设计思想 
  - 用户进程划分：先按段划分，每一段再按页面划分 
- 逻辑地址：  

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\段页式存储管理方案.png" alt="段页式存储管理方案" style="zoom:30%;" />

- 内存划分：同页式存储管理方案 
- 内存分配：以页为单位进行分配
- 数据结构及有关操作 
  - 段表：记录了每一段的页表始址和页表长度 
  - 页表：记录了逻辑页号与页框号的对应关系 
    - 每一段有一张页表，一个进程有多个页表 
  - 空闲区管理：同页式管理 
  - 内存分配、回收：同页式管理

#### 基本内存管理方案小结

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\基本内存管理方案小结.png" alt="基本内存管理方案小结" style="zoom:30%;" />

### 7.6 交换技术 SWAPPING

#### 内存“扩充”技术

- 内存紧缩技术（例如：可变分区） 
- 覆盖技术 overlaying 
- 交换技术 swapping 
- 虚拟存储技术 virtual memory

#### 覆盖技术（OVERLAYING）

- 解决的问题 → 程序大小超过物理内存总和 
- 程序执行过程中，程序的不同部分在内存中相互替代 
  - 按照其自身的逻辑结构，将那些不会同时执行的程序段共享同一块内存区域 
    - 要求程序各模块之间有明确的调用结构 
- 程序员声明覆盖结构，操作系统完成自动覆盖
- 主要用于早期的操作系统

#### 交换技术（SWAPPING）

- 设计思想 
  - 内存空间紧张时，系统将内存中某些进程暂时移到外存，把外存中某些进程换进内存，占据前者所占用的区域（进程在内存与磁盘之间的动态调度）

#### 关于讨论的问题

- 进程的哪些内容要交换到磁盘？
  - 运行时创建或修改的内容：栈和堆
- 会遇到什么困难？ 
- 在磁盘的什么位置保存被换出的进程？ 
  - 交换区：一般系统会指定一块特殊的磁盘区域作为交换空间（swap space），包含连续的磁道， 操作系统可以使用底层的磁盘读写操作对其高效访问
- 交换时机？ 
  - 只要不用就换出（很少再用）；内存空间不够或有不够的危险时换出与调度器结合使用
- 如何选择被换出的进程？
  - 考虑进程的各种属性；不应换出处于等待I/O状态的进程  
- 如何处理进程空间增长？

## 第8讲 存储模型2——虚拟存储技术

### 8.1 虚拟存储技术 VIRTUAL MEMORY

- 所谓虚拟存储技术是指：当进程运行时，先将其一部分装入内存，另一部分暂留在磁盘，当要执行的指令或访问的数据不在内存时，由操作系统自动完成将它们从磁盘调入内存的工作 
- 虚拟地址空间即为分配给进程的虚拟内存 
- 虚拟地址是在虚拟内存中指令或数据的位置， 该位置可以被访问，仿佛它是内存的一部分

#### 虚存与存储体系

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\存储器的层次结构.png" alt="存储器的层次结构" style="zoom:30%;" />

- 把内存与磁盘有机地结合起来使用，从而得到一个容量很大的“内存”，即虚存 
- 虚存是对内存的抽象，构建在存储体系之上，由操作系统协调各存储器的使用 
- 虚存提供了一个比物理内存空间大得多的地址空间

#### 存储保护

- 确保每个进程有独立的地址空间 
- 确保进程访问合法的地址范围 
  - 防止地址越界
- 确保进程的操作是合法的
  - 防止访问越权

#### 虚拟页式（PAGING）

- 虚拟存储技术 + 页式存储管理方案  → 虚拟页式存储管理系统 
- 基本思想 
  - 进程开始运行之前，不是装入全部页面， 而是装入一个或零个页面 
  - 之后，根据进程运行的需要，动态装入其他页面 
  - 当内存空间已满，而又需要装入新的页面时，则根据某种算法置换内存中的某个页面，以便装入新的页面

### 8.2 页表级页表项的设计

#### 页表表项设计

- 通常，页表项是硬件设计的

- 页表由页表项组成 
  - 页框号、有效位、访问位、修改位、保护位 
    - 页框号（内存块号、物理页面号、页帧号） 
    - 有效位（驻留位、中断位）：表示该页是在内存还是在磁盘 
    - 访问位：引用位 
    - 修改位：此页在内存中是否被修改过 
    - 保护位：读/可读写

#### 关于页表

- 32位虚拟地址空间的页表规模？ 
  - 页面大小为4K；页表项大小为4字节 
    - 则：一个进程地址空间有 2^20 页 （2^32 / 2^12）
      - 其页表需要占 1024 页（页表页） （2^20 / (4K / 4)）
- 64位虚拟地址空间 
  - 页面大小为4K；页表项大小为8字节 
    - 页表规模： 32,000 TB 
- 页表页在内存中若不连续存放，则需要引入页表页的地址索引表 → 页目录（Page Directory）

#### 二级页表结构及地址映射

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\二级页表结构及地址映射.png" alt="二级页表结构及地址映射" style="zoom:30%;" />

#### 引入反转（倒排）页表

- 地址转换 
  - 从虚拟地址空间出发：虚拟地址 → 查页表 → 得 到页框号 → 形成物理地址 
  - 每个进程一张页表 
- 解决思路：物理空间是有限的
  - 从物理地址空间出发，系统建立一张页表 
  - 页表项记录进程i的某虚拟地址(虚页号)与页框号的映射关系

### 8.3 地址转换过程及TLB引入

#### MMU：内存管理单元

<img src="D:\Program Files\sts-bundle\source\OperatingSystem\image\内存管理单元.png" alt="内存管理单元" style="zoom:30%;" />

### 8.4 页错误（页故障）PAGE FAULT

### 8.5 软件相关策略

### 8.6 置换算法1

### 8.7 置换算法2——工作集算法

### 8.8 其他相关技术