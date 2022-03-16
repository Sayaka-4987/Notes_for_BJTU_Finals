# 计算机体系结构笔记

期末考试开卷，但是是英文试卷，平时50%，期末50%

## Chapter 1. 计算机体系结构的基本原理

### 多级层次结构

计算机体系结构是程序员所看到的计算机的属性即概念性结构与功能特性；

* 具有相同计算机系统结构的计算机可以采用不同的计算机组织。例如相同的指令系统可以顺序执行，也可以重叠执行。
* 一种计算机组织可以采用不同的计算机实现。
* 随着计算机技术的迅速发展，计算机系统结构，组织和硬件之间的界限变得越来越模糊。

<img src="media/image-20220304141637043.webp" alt="image-20220304141637043" style="zoom: 40%;" />

### 性能评估

#### 衡量性能的标准

- 用户的角度：程序运行时间短 Computer user consider: a computer is faster when a program runs in less time. 
  
  - Response time (响应时间)： the time between the start and the completion of an event—also referred to as execution time (执行时间).

- 计算机中心的角度：在一小时内能做更多工作 Computer center manager consider: a computer is faster when  it completes more jobs in an hour. 
  
  - Throughput (吞吐量): the total amount of work done in a given time.

#### 测量性能的方法

##### 响应时间（Response time）

计算机完成某一任务所花费的全部时间（latency），包括磁盘访问（disk accesses）、存储器访问（memory accesses）、输入输出（input/output activities）、操作系统开销（operating system overhead）等。

在计算机内存中间同时存放几道相互独立的程序（multiprogramming），相互穿插运行，因此一般不必要单单减少某个程序的执行时间（elapsed time）。

CPU时间是CPU执行所给定的程序所花费的时间，不包括含I/O等待时间以及运行其他程序的时间。

##### 用户CPU时间（User CPU time）

用户程序所耗费的CPU时间。

##### 系统CPU时间（System CPU time）

用户程序运行期间操作系统耗费的时间。

#### 选择程序来评估性能

1. 真实应用（Real applications）

2. 核心程序（Kernels）

3. 玩具测试基准（Toy benchmarks）

4. 合成测试基准（Synthetic benchmarks）

### 量化设计准则

#### 大概率事件优先原则

对于大概率事件（最常见的事件），赋予它优先的处理权和资源使用权，以获得全局的最优结果。

#### 阿姆达尔定律

加快某部件执行速度所获得的系统性能提高（加速比）与该部件在系统中的总执行时间的比例有关。

​                                             **$系统加速比 = \frac{改进后系统性能}{改进前系统性能} = \frac{改进前总执行时间}{改进后总执行时间}$** 

- 下图的 $Fraction_{enhanced}$（简写 $F_e$）是**可改进比例**，指可改进部分在原系统执行时间中所占的比例； 
- $Speedup_{enhanced}$（简写 $S_e$）是**部件加速比**，指可改进部分改进后的性能提高； 
- 阿姆达尔定律表示为 $S_{overall} = \frac{T0}{Te} = \frac{1}{(1-Fe)+ \frac{Fe}{Se}}$ ； 

<img src="media/image-20220304151927822.webp" alt="image-20220304151927822" style="zoom:40%;" />

- 需要注意的是 Fe 是**时间比时间**的比例；

#### CPU性能方程

计算机运行基于固定频率的时钟信号（clock periods, clocks, cycles, clock cycles），主频=1÷时钟周期，因此

$CPU时间 = 完成这一任务的时钟周期数 × 每个时钟周期时间$ 或  $CPU时间 = \frac{完成这一任务的时钟周期数}{主频}$ 

常用方程的形式是 CPU 时间 = 指令条数 × 每条指令时钟周期数 × 每个时钟周期时间

 = IC(Instruction Count) × CPI(Cycles Per Instruction) × CC(Clock cycle time)

#### 访问的局部性原理

程序倾向于重用刚用过的数据和指令，90%的时间运行于10%的程序上
因此，可根据最近访问预测未来会用到哪些指令和数据。

程序局部性包括：

1. 程序的时间局部性：程序即将用到的信息很可能就是目前正在使用的信息。
2. 程序的空间局部性：程序即将用到的信息很可能与目前正在使用的信息在空间上相邻或者临近。

### 按指令集和数据流分类

##### 单指令流单数据流 (SISD, single instruction stream over a single data stream)

<img src="media/image-20220308115819360.webp" alt="image-20220308115819360" style="zoom: 33%;" />

##### 单指令流多数据流 (SIMD, single instruction stream over multiple data stream)

<img src="media/image-20220308115841394.webp" alt="image-20220308115841394" style="zoom:33%;" />

##### 多指令流多数据流 (MIMD, multiple instruction over multiple data streams)

<img src="media/image-20220308115925196.webp" alt="image-20220308115925196" style="zoom:33%;" />

多指令流单数据流 (MISD, multiple instruction streams and a single data stream) 

<img src="media/image-20220308115858617.webp" alt="image-20220308115858617" style="zoom: 33%;" />

## Chapter 2. 指令集体系结构

- 指令集：计算机所有指令的集合。一条指令由操作码与地址码构成。
  
  - 指令的操作十分简单，其操作由操作码编码表示。
  
  - 每个操作需要的操作数个数为0-3个不等。

- 操作数是一些存储单元的地址；
  
  - 典型的存储单元通常有：主存、寄存器、堆栈和累加器。

- 操作数地址隐含表示或显式表示。

指令集是计算机体系结构的重要部分（直接挂钩 CPI 和 IC），定义CPU的功能，也是程序员控制CPU的手段

### 计算机指令集结构分类

* 在CPU中操作数的存储方法；
* 指令中显式表示的操作数个数；
* 操作数的寻址方式；
* 指令集所提供的操作类型；
* 操作数的类型和大小。

**CPU内部存储单元的类型是最基本的区别**：

* 堆栈型指令集结构（Stack architecture）
  
  * 操作数隐式放在堆栈结构的栈顶

* 累加器型指令集结构（Accumulator architecture）
  
  * 操作数中至少一个隐式放在累加器中

* **通用寄存器型指令集结构**（**GPR**，**G**eneral-**P**urpose **R**egister architectures）
  
  * 或者是寄存器，或者是存储器位置

总结：

| 指令集结构类型 | 优点                    | 缺点                                            |
| ------- | --------------------- | --------------------------------------------- |
| 堆栈型     | 是一种表示计算的简单模型；<br>指令短小 | 不能随机访问堆栈，从而很难生成有效代码。<br>同时，由于堆栈是瓶颈，所以很难被高效地实现 |
| 累加器型    | 减小了机器的内部状态；指令短小       | 由于累加器是唯一的暂存器，存储器通信开销最大                        |
| 寄存器型    | 易于生成高效的目标代码（所以是最常用）   | 所有操作数均需命名且显式表示，指令比较长                          |

**根据两种寄存器类型分**：

1. 寄存器-存储器体系结构：操作数可以来自存储器

2. 寄存器-寄存器体系：所有操作数都是来自寄存器、只有load与store能够访问存储器

3. 存储器-存储器体系结构：目前现实中没有

<img title="" src="./media/高级语言的C=A+B.webp" alt="" data-align="center" width="591">

总结：

| 寄存器类型    | 优点                                                                       | 缺点                                                                                                     |
| -------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| 寄存器-寄存器型 | 简单，指令字长固定，是一种简单的代码生成模型，各种指令的执行时钟周期数相近。<br>**SPARC, MIPS, PowerPC属于这个类型** | 和指令中含有对存储器操作数访问的结构相比，指令条数多（要使用load/store），因而其目标代码较大                                                    |
| 寄存器-存储器型 | 可以直接对存储器操作数进行访问，容易对指令进行编码，且其目标代码较小<br>**8086属于这个类型**                     | 指令中的操作数类型不同。在一条指令中同时对一个寄存器操作数和存储器操作数进行编码，将限制指令所能够表示的寄存器个数。由于指令的操作数可以存储在不同类型的存储器单元，所以每条指令的执行时钟周期数也不尽相同。 |
| 存储器-存储器型 | 是一种最紧密的编码方式，无需“浪费”寄存器保存变量。                                               | 指令字长多种多样。每条指令的执行时钟周期数也大不一样，对存储器的频繁访问将导致存储器访问瓶颈问题。                                                      |

### 内存地址的解释

#### 大端序和小端序

例：Suppose you have the 32-bit hexadecimal value 87654321 stored as a 32-bit word in byte-addressable memory at byte location 480, 481, 482, and 483.
a) What will be arranged in memory 480~483 according to Little Endian and Big Endian?
b) In which bytes order to Intel 80x86 systems store multibyte values?

答：8086是小端序；

| 存放顺序 | 480H | 481H | 482H | 483H |
| ---- | ---- | ---- | ---- | ---- |
| 大端序  | 87H  | 65H  | 43H  | 21H  |
| 小端序  | 21H  | 43H  | 65H  | 87H  |

##### 记住取数取的是首地址

<img src="./media/地址的整数倍位置.webp">
