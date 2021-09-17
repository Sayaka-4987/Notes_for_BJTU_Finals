# 汇编与接口技术

期末考试开卷、50% + 平时成绩20% + 常规实验15% + 研究性实验15%

地点：YF312 或九教 4 层实验室

### 这份笔记画 ※ 的都要背！

### 做实验 **<u>需要在上机前就编写完程序</u>** ！！



## 汇编部分的网络参考资料

[MASM debug 命令](https://www.cnblogs.com/tiger2soft/p/5094917.html) 

[【8086汇编基础】05--常用函数库文件--emu8086.inc](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758059.html)



## 1. 基础知识

### 1.2 硬件接口

~~（这段抄的 zll 的，后面还一段也是，不想打字惹）~~

CPU与接口交换数据的方式：

1. **查询方式**

信息交换的控制完全由主机执行程序实现，程序查询方式接口中设置一个数据缓冲寄存器（数据端口）和一个设备状态寄存器（状态顿口）；

主机进行 I/O 操作时，先发出询问信号，读取设备的状态，并根据设备状态决定下一步操作究竟是进行数据传送还是等待。在这种控制方式下，CPU 一旦启动 I/O，就必须停止现行程序的运行，并在现行程序中插入一段程序；

CPU 要花费很多时间来查询和等待，而且在一段时间内只能与一台外设交换信息，效率低；

2. **中断方式**

CPU在程序中安排好于某个时刻启动某台外设，然后CPU继续执行原来的程序，不需要像查询方式一样一直等待外设准备就绪；

一旦外设完成数据传送的准备工作，就主动向CPU发出中断请求，请求CPU为自己服务；在可以响应中断的条件下，CPU暂时中止正在执行的程序，转去执行中断服务程序为外设服务，在终端服务程序中完成一次主机与外设之间的数据传送，传送完成后，CPU返回原来的程序；

3. **DMA 方式**

主存和 DMA 接口之间有一条直接的数据通路；

DMA 方式传送数据不需要经过CPU，因此不必中断现行程序，I/O与主机并行工作，程序和传送并行工作。



## 2. 80x86 计算机组织

本课程以 16 位的 8086 作为教学对象；



### 2.1 计算机系统

微处理器就是把 **运算器、寄存器组、控制器** 集成在一个超大规模集成电路芯片上的功能部件；

存储器是微机存放和记忆程序、数据的部件，分为内存和外存；

外设可以分为两部分，一类是直接为计算机运行服务的，如键盘、鼠标；另外一类是暂时不影响计算机的外设，如打印机，显示器；

系统总线包括数据总线（传送数据）、地址总线（指出数据来源或目的地）、控制总线（传送 CPU 对存储器或 I/O 设备的控制命令，和 I/O 设备对 CPU 的请求服务信号），总线工作由系统总线控制模块指挥；



### 2.2 8086 微处理器

8086 的基本性能指标：

1. 16位微处理器；
2. 16根数据线、20根地址线，可寻址的地址空间达 1MB (220Byte＝1MB)；
3. 可以和浮点运算器、I/O 处理器或其他处理器组成多处理器系统，提高系统的数据吞吐能力、处理能力

<img src=".\media\微信截图_20210908104652.webp" style="zoom:33%;" />

通用寄存器包括 8 个 16 位寄存器：AX、BX、CX、DX、SP、BP、SI 和 DI；
标志寄存器为 FLAGS ，是一个 16 位的寄存器；

总线接口部件：负责管理与系统总线的接口，负责对存储器和外设访问；包括指令队列、指令指针、段寄存器、地址加法器和总线控制逻辑；

指令执行部件：包括 ALU 、通用寄存器、标志寄存器和控制电路；负责指令译码、数据运算和指令执行；

指令执行的两个主要阶段：取指和执行，其中取指：从主存取出指令代码进入指令队列，执行：译码指令、并发出有关控制信号实现指令功能；

8086 处理器的指令读取实质是 **指令预取**：8086 处理器维护着长度为 6 个字节的指令队列；EU 单元译码、执行指令，同时 BIU 单元读取后续指令，BIU 和 EU 两个单元相互独立，可以并行操作；



#### ※ 8086 CPU 寄存器分组表

<img src=".\media\8086寄存器.webp" style="zoom:67%;" />

以 [ BX+SI ] 为例：BX 为基址、SI 为变址；

##### 数据寄存器 AX,  BX, CX, DX

- 数据寄存器用来保存 **操作数或运算结果** 等；

  - AX 是累加器，用于算术、逻辑运算以及外设传送信息等；
  - BX 是基址寄存器，常用于存放存储器地址；
  - CX 是计数器，作为循环或串操作等指令中的隐含计数器；
  - DX 是数据寄存器，用来存放双字数据的高16位或存放外设端口地址；

- 以 AX 为例，它对应的 AH, AL 指的是 AX 的高位和低位；

  

##### 指针寄存器 SP, BP 

- 指针寄存器和变址寄存器用于存放 **某个存储单元的偏移地址**；

  - SP 用于存放当前堆栈中栈顶的偏移地址；

  - BP 用于存放堆栈段中某一存储单元的偏移地址；

    

##### 变址寄存器 SI, DI

- 在串操作中， SI 和 DI 都具有自动增量或减量的功能，它的目的操作数和源操作数都在连续空间；

  - SI 用于存源地址（操作完其值不改变）；
  - DI 用于存目的地址，操作完其值发生改变；



##### 控制寄存器 IP, FLAGS 

- 2 个控制寄存器

  - IP 指令指针寄存器保存下一次将要取出指令的偏移地址，IP 的内容由微处理器硬件自动设置，不能用指令直接修改，有一些指令可以改变 IP 的值，如转移指令、子程序调用指令等；
  - FLAGS 标志寄存器包含 9 个标志位，保存一条指令执行后，CPU 所处状态信息及运算结果的特征；

  

##### 段寄存器 CS, DS, SS, ES 

- 段寄存器用来确定 **该段在内存中的起始地址**，8086 有 4 个 16 位的段寄存器

  - 代码段 CS 主要存放运行的程序
  - 数据段 DS 存放运行程序所用的数据
  - 堆栈段 SS 定义了堆栈的所在区域
  - 附加段 ES 是附加的数据段，它是辅助数据区，在串操作指令中，用到的目的串必定存放在附加段中

  

#### 通用寄存器的专门用途

<img src=".\media\通用寄存器的专门用途.webp" style="zoom: 33%;" />



#### 堆栈的两种访问方式

1. 随机访问方式：用 BP 寄存器来指示随机访问地址；

2. 先进后出访问方式：用 SP 寄存器来表示栈顶；



#### 标志寄存器的格式及各位的含义

<img src=".\media\标志寄存器的格式及各位的含义.webp" style="zoom:50%;" />

##### 进位标志 CF（Carry Flag）

当 **无符号整数** 加减运算的最高有效位有进位（加法）或借位（减法）时，CF＝1，否则 CF＝0；



##### 奇偶标志 PF（Parity Flag）

当运算结果的 **最低字节**（注：就是看最后 8 位，1 个字节 Byte 8 位 bit，8 位的结果看全部 8 位，16 位的结果看后 8 位）中， 1 的个数为零或偶数时，PF＝1；否则 PF＝0；



##### 辅助进位标志 AF（Auxiliary Carry Flag）

当运算结果的 **最低字节** 低位的一半向高位（看最后 8 位的低 4 位向高 4 位）有进位或借位时，AF = 1；

该标志与操作数长度无关，只看最低字节中间那两位就行；



##### 零标志 ZF（Zero Flag）

运算结果为0，则 ZF＝1，否则 ZF＝0；



##### 符号标志 SF（Sign Flag）

运算结果最高位为1，则 SF＝1，否则 SF＝0；



##### 溢出标志 OF（Overflow Flag）

当 **有符号整数** 加减结果有溢出，则 OF＝1，否则 OF＝0；

> 对于程序员，无符号数关心进位，有符号数关心溢出，相同符号数相加后符号相反（如正+正=负），则有溢出；
>
> 对于处理器硬件判断，最高位和次高位同时进位或不进位，则无溢出；反之则有溢出；



##### 方向标志 DF（Direction Flag）

仅用于串操作指令，控制地址的变化方向：

1. 设置 DF＝0，每次串操作后的存储器地址就自动增加，即从低地址向高地址处理数据串；
2. 设置 DF＝1，每次串操作后的存储器地址就自动减少，即从高地址向低地址处理数据串；
3. 可以执行 CLD 指令设置 DF＝0；执行 STD 指令设置 DF＝1 ；



##### 中断允许标志 IF（Interrupt-enable Flag）

主要针对外中断中可屏蔽中断的开放或禁止：

1. 当 IF=1 时，CPU 允许响应可屏蔽中断，中断当前程序，转去执行中断处理程序；
2. 当 IF＝0 时，则不允许响应可屏蔽中断；
3. 可以执行 STI 指令设置 IF = 1；执行 CLI 指令设置 IF = 0；



##### 追踪标志 TF（Trace Flag）

用于单步调试程序：

1. 当 TF=1 时，在执行完一条指令后，产生单步中断；这在DEBUG调试程序状态下，可以使指令单步运行，可逐一检查各寄存器内容，标志状态、存储器的检查或修改等等；
2. 追踪标志 TF=1 时为调试程序时所用，当程序调试成功后让 TF=0，CPU正常工作不产生单步中断；



### 2.3 存储器地址

#### 存储器的组织：8086 实模式

采用分段方式，20位的物理地址 由 **16位的段地址** 和 **16位的偏移地址** 形成，每个段的最大寻址空间为 64KB；

段地址必须是 16 的倍数，即（二进制写法）末尾 4 位必须为 0；



#### 存储单元的内容

课件记为存储地址加括号”( )” ，编程时用 []；

X 单元中存放着 Y，而 Y 是另一个存储单元的地址，则 Y 单元的内容表示为 (Y) = ((X))；

字单元由**两个字节单元**组成，其地址采用它的低地址来表示；

字存入存储器：低位字节存入低地址单元，高位字节存入高地址单元；



#### 储存单元地址例题

<img src=".\media\储存单元地址例题.webp" alt="image-20210908121210248" style="zoom:50%;" />



#### 储存器的分段

8086 的内部寄存器是 16 位，地址是 20 位，地址宽度大于字长，显然不能用16位的寄存器仅一次操作实现对 2^20^＝1M 字节单元的寻址；

- 20 根地址线，地址范围 00000H ~ FFFFFH （1MB）
- 机器字长 16 位，仅能表示地址范围  0000H ~ FFFFH（64KB)
- 物理地址：每一个存储单元有一个唯一的 20 位地址



为此引入存储器“分段”的概念，即把 1M 字节内存空间，分成最大 64KB 的段，每段可用 16 位寄存器进行寻址；



段地址：表示一个段的开始，段地址一般存在段寄存器；

偏移地址：在段内相对于段起始地址的偏移值；

逻辑地址：**段地址：偏移地址**



<img src=".\media\储存器的逻辑地址与物理地址.webp" style="zoom:38%;" />



物理地址：每一个存储单元有一个唯一的 20 位地址，表示范围：00000H～FFFFFH；

物理地址形成：**段地址左移 4 位，再加上偏移地址值**；

<img src=".\media\物理地址的形成.webp" style="zoom:38%;" />



- 逻辑地址的形式：指令操作数，指令间的相对地址
- 物理地址：MEM 的绝对地址
- 逻辑地址到物理地址的转换过程因 CPU 架构的不同而不同



##### 例：8086 逻辑地址与物理地址的转换

<img src=".\media\逻辑地址与物理地址的转换.webp" style="zoom:38%;" />



> 这里左移的 4 位是二进制位；如果用十六进制表达地址就是左移一位；
>
> 左移 4 位还可以表达为乘以 16，即：**物理地址 = 段地址 × 16D + 偏移地址** （或 = 段地址 × 10H + 偏移地址）



例：设某操作数存放在数据段，DS = 250AH，数据所在单元的偏移地址 = 0204H；则该操作数所在单元的物理地址为（    ）；

解：此类题目会设坑，给出一堆寄存器，答题需要先找**哪个是段地址**，需要注意偏移地址和段基址对应关系；本题提示了操作数存放在数据段，所以 DS 就是段地址，得物理地址 = **252A4H**；



<img src=".\media\段寄存器分配.webp" style="zoom:38%;" />



#### 偏移地址的组成

16 位有效地址：

- EA＝基址寄存器＋变址寄存器＋位移量
  - 基址寄存器：BX 或 BP，**一般由基址寄存器决定哪个段寄存器作为段指针**
  - 变址寄存器：SI 或 DI
  - 位移量：8 或 16 位有符号值（可正可负）



## 补充资料：8086 汇编语言初学者教程

### `MOV` 指令：拷贝

- 功能：将第二个操作数（源）拷贝到第一个操作数（目的）指定位置；
- 源操作数可以是立即数，通用寄存器或者内存单元；
- 目的寄存器可以是通用寄存器或者内存单元；
- 源和目的必须是同样大小，要么都是字节要么都是字；



#### `MOV` 指令的操作类型

- **REG**：AX, BX, CX, DX, AH, AL, BL, BH, CH, CL, DH, DL, DI, SI, BP, SP；
- **memory**：[BX], [BX+SI+7], 变量等；
- **immediate**：5, -24, 3Fh, 10001101b 等；
- **SREG**：DS, ES, SS, 注意 **CS 只能作为操作源；**

```assembly
MOV REG, memory
MOV memory, REG
MOV REG, REG
MOV memory, immediate
MOV REG, immediate

; MOV 只支持如下段寄存器：
MOV SREG, memory
MOV memory, SREG
MOV REG, SREG
MOV SREG, REG
```



### 声明变量

编译器支持两种变量： **BYTE**（字节） 和 **WORD**（字）；

- name 可以是任何字母与数字构成，但是必须由字母开头；
- 可以通过不命名来声明一个没有名称的的变量（这个变量只有地址，没有名称）；
- value 可以是任何数值，支持三种进制（十六进制,二进制和十进制），可以使用 `?` 符号表示初始值没有确定；

```assembly
name DB value ; 名称 DB 值, DB = stays for Define Byte.
name DW value ; 名称 DW 值, DW = stays for Define Word.
```

例：（COM 文件）

```assembly
#MAKE_COM#
ORG 100h

MOV AL, var1
MOV BX, var2

RET    ; stops the program.

VAR1 DB 7
var2 DW 1234h
```



### 声明常量

常量同变量很相似，但是它一直存在。定义一个变量之后，它的值不会改变；

使用 `EQU` 语句定义常量；

格式：**name equ <任意表达式>**

```assembly
k EQU 5
MOV AX, k    ; 等价于 MOV AX, 5
```



### `ORG` 伪指令

`ORG` 是一个编译指令，它告诉编译器如何处理源代码；

`ORG 100` 通知编译器，可执行程序将被调入偏移量是 100h（256 字节）的位置，从而可以计算出所有变量的正确地址，然后用这些地址（偏移量）来代替变量名称；

- 这些指令不会真正的编译为任何机器代码，本身也不占内存空间；

- 可执行程序总是被装入偏移量 **100h**；

- EXE文件调入在偏移量 0000 的位置，它使用特定的段保存变量

#### `ORG` 的使用

- `ORG 100` 用在 COM 文件的开头；
- 还可以用于指明下一条汇编语句的偏移地址 ：


```assembly
SEG1    SEGMENT
        ORG   10         ; 设置$为10，此段目标代码从偏移地址10开始
        VAR1  DW  1234H  ; VAR1的偏移地址为10
        ORG   20         ; 在10和20地址之间是没有指令的
        VAR2  DW  5678H
        ORG   $+8        ; $增加8，即在5678H之后空出8个字节
        VAR3  DW  1357H
SEG1    ENDS

        ORG  100H
START:  ……
```



### 数组

- 数组可以看作是变量链，一个字符串本质是一个字节数组；
- 对于字符串，字符串是 byte 的数组，byte 是最小单位；
- 对于双字节整数，word是最小单位

#### 定义数组

```assembly
a DB 48h, 65h, 6Ch, 6Ch, 6Fh, 00h
b DB 'Hello', 0
```



### `DUP` 指令：重复

- 格式：**number DUP ( value(s) )** 
- 其中 number 为重复的数量（任意常数），value 为将要复制的表达式

```assembly
c DB 5 DUP(9)    ; 等价于 c DB 9, 9, 9, 9, 9 
d DB 5 DUP(1, 2) ; 等价于 d DB 1, 2, 1, 2, 1, 2, 1, 2, 1, 2 
```



### `LEA` 指令或 `OFFSET` 指令：取得变量地址

```assembly
#MAKE_COM#
ORG 100h
  
MOV AL, VAR1
LEA BX, VAR1  			; 等效于 MOV BX, OFFSET VAR1  

RET    ; stops the program.
   
VAR1 DB 12H
```



### `INT` 指令：软件中断

中断是一系列功能调用。比如，在打印机上输出一个字符，只需要简单的调用中断，它将完成所有的事情；

这些功能调用称作软件中断；

需要使用 INT 指令触发一个软件中断；

格式：**INT value**

其中 value 的取值范围是从 0 到 255 （或者 0 到 0FFH），通常使用十六进制写法；

每一个中断都有子功能，在调用一个中断的子功能之前，需要设置  AH 寄存器（通常使用 AH）；


每一个中断最多可以拥有 256 个子功能（因此，总共可以有 256*256＝65536 个功能调用）；



例：使用中断 **0Eh** 子功能输出字符串 ‘Hello!' 

```assembly
#MAKE_COM# ; 生成com文件的指令

ORG 100h

MOV AH, 0Eh ; 选择子功能 int 10h/0Eh，输出放在 AL 寄存器中的 ASCII 码对应的字符

MOV AL, 'H' ; ASCII码: 72
INT 10h ; 输出

MOV AL, 'e' ; ASCII 码: 101
INT 10h ; 输出

MOV AL, 'l' ; ASCII 码: 108
INT 10h ; 输出

MOV AL, 'l' ; ASCII 码: 108
INT 10h ; 输出

MOV AL, 'o' ; ASCII 码: 111
INT 10h ; 输出

MOV AL, '!' ; ASCII 码: 33
INT 10h ; 输出

RET ; 返回操作系统
```



#### 模拟器支持的中断功能表（全英文，没翻译）

| 指令及其功能                                                 | 输入                                                         | 输出                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **INT 10h** / **AH = 00h** \- set video mode.                | **AL** = desired video mode.<br>**00h** - Text mode 40x25, 16 colors, 8 pages. |                                                              |
| **INT 10h** / **AH = 01h** - set text-mode cursor shape.     | **CH** = cursor start line (bits 0-4) and options (bits 5-7). <br>**CL** = bottom cursor line (bits 0-4).<br>When bits 6-5 of CH are set to **00**, the cursor is visible, to hide a cursor set these bits to **01** |                                                              |
| **INT 10h** / **AH = 02h** - set cursor position.            | **DH** = row.<br/>**DL** = column.<br/>**BH** = page number (0..7). |                                                              |
| **INT 10h** / **AH = 03h** - get cursor position and size.   | **BH** = page number.                                        | **DH** = row.<br/>**DL** = column.<br/>**CH** = cursor start line.<br/>**CL** = cursor bottom line. |
| **INT 10h** / **AH = 05h** - select active video page.       | **AL** = new page number (0..7).                             |                                                              |
| **INT 10h** / **AH = 06h** - scroll up window.<br/>**INT 10h** / **AH = 07h** - scroll down window. | **AL** = number of lines by which to scroll (00h = clear entire window).<br/>**BH** = [attribute](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib) used to write blank lines at bottom of window.<br/>**CH, CL** = row, column of window's upper left corner.<br/>**DH, DL** = row, column of window's lower right corner. |                                                              |
| **INT 10h** / **AH = 08h** - read character and [attribute](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib) at cursor position. | **BH** = page number.                                        | **AH** = [attribute](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib).<br/>**AL** = character. |
| **INT 10h** / **AH = 09h** - write character and [attribute](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib) at cursor position. | **AL** = character to display.<br/>**BH** = page number.<br/>**BL** = [attribute](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib).<br/>**CX** = number of times to write character. |                                                              |
| **INT 10h** / **AH = 0Ah** - write character only at cursor position. | **AL** = character to display.<br/>**BH** = page number.<br/>**CX** = number of times to write character. |                                                              |
| **INT 10h** / **AH = 0Eh** - teletype output.                | **AL** = character to write.<br>This functions displays a character on the screen, advancing the  cursor and scrolling the screen as necessary. <br>The printing is always  done to current active page. |                                                              |
| **INT 10h** / **AH = 13h** - write string.                   | **AL** = write mode:<br/>  **bit 0**: update cursor after writing;<br/>  **bit 1**: string contains [attributes](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib).<br/>**BH** = page number.<br/>**BL** = [attribute](https://www.cnblogs.com/QuLory/archive/2012/11/07/2758054.html#attrib) if string contains only characters (bit 1 of AL is zero).<br/>**CX** = number of characters in string (attributes are not counted).<br/>**DL,DH** = column, row at which to start writing.<br/>**ES:BP** points to string to be printed. |                                                              |
| **INT 10h** / **AX = 1003h** - toggle intensity/blinking.    | **BL** = write mode:<br/>  **0**: enable intensive colors.<br/>  **1**: enable blinking (not supported by emulator!).<br/>**BH** = 0 (to avoid problems on some adapters). |                                                              |
| **INT 11h** - get BIOS equipment list.                       | **AX** = BIOS equipment list word, actually this call returns the contents of the word at 0040h:0010h. |                                                              |
| **INT 12h** - get memory size.                               | **AX** = kilobytes of contiguous memory starting at  absolute address 00000h,	this call returns the contents of the word at  0040h:0013h. |                                                              |
| **INT 13h** / **AH = 00h** - reset disk system               | (currently this call doesn't do anything).                   |                                                              |
| **INT 13h** / **AH = 02h** - read disk sectors into memory.<br/>**INT 13h** / **AH = 03h** - write disk sectors. | **AL** = number of sectors to read/write (must be nonzero)<br/>**CH** = cylinder number (0..79).<br/>**CL** = sector number (1..18).<br/>**DH** = head number (0..1).<br/>**DL** = drive number (0..3 , depends on quantity of FLOPPY_? files).<br/>**ES:BX** points to data buffer. | **CF** set on error.<br/>**CF** clear if successful.<br/>**AH** = status (0 - if successful).<br/>**AL** = number of sectors transferred. |
| **INT 15h** / **AH = 86h** - BIOS wait function.             | **CX:DX** = interval in microseconds                         | **CF** clear if successful (wait interval elapsed),<br/>**CF** set on error or when wait function is already in progress. |
| **INT 16h** / **AH = 00h** - get keystroke from keyboard (no echo). |                                                              | **AH** = BIOS scan code.<br/>**AL** = ASCII character.<br>(if a keystroke is present, it is removed from the keyboard buffer). |
| **INT 16h** / **AH = 01h** - check for keystroke in keyboard buffer. |                                                              | **ZF = 1** if keystroke is not available.<br/>**ZF = 0** if keystroke available.<br/>**AH** = BIOS scan code.<br/>**AL** = ASCII character.<br/>(if a keystroke is present, it is not removed from the keyboard buffer). |
| **INT 19h** - system reboot.                                 |                                                              |                                                              |
| **INT 1Ah** / **AH = 00h** - get system time.                |                                                              | **CX:DX** = number of clock ticks since midnight.<br/>**AL** = midnight counter, advanced each time midnight passes. |
| **INT 20h** - exit to operating system.                      |                                                              |                                                              |
| **INT 21h** / **AH=09h** - output of a string at DS:DX.      |                                                              |                                                              |
| **INT 21h** / **AH=0Ah** - input of a string to DS:DX, fist byte is buffer size, second byte is number of chars actually read. |                                                              |                                                              |
| **INT 21h** / **AH=4Ch** - exit to operating system.         |                                                              |                                                              |
| **INT 21h** / **AH=01h** - read character from standard input, with echo, result is stored in AL. |                                                              |                                                              |
| **INT 21h** / **AH=02h** - write character to standard output, DL = character to write, after execution AL = DL. |                                                              |                                                              |



#### 颜色表

字符属性为 8 位值，低 4 位设置前景色，高 4 位设置背景色；

但是，不支持背景色闪烁；

```assembly
HEX    BIN       COLOR
-----------------------
0      0000      black
1      0001      blue
2      0010      green
3      0011      cyan
4      0100      red
5      0101      magenta
6      0110      brown
7      0111      light gray
8      1000      dark gray
9      1001      light blue
A      1010      light green
B      1011      light cyan
C      1100      light red
D      1101      light magenta
E      1110      yellow
F      1111      white
```



### 常用函数库 `emu8086.inc`

编译器会自动在你源程序所在的文件夹中查找你引用的文件，如果没有找到，它将搜索 `Inc` 文件夹；

使用 **emu8086.inc** 头文件需要在程序开头加上：

```assembly
include 'emu8086.inc'
```

**emu8086.inc** 定义了一些方便的输入输出宏：

```assembly
PUTC char ; 将一个ascii字符输出到光标当前位值，只有一个参数的宏

GOTOXY col, row ; 设置当前光标位置，有两个参数

PRINT string ; 输出字符串，一个参数

PRINTN string ; 输出字符串，一个参数。与 PRINT 功能相同，不同在于输出之后自动回车

CURSOROFF ; 关闭文本光标

CURSORON ; 打开文本光标

PRINT_STRING ; 在当前光标位置输出一个字符串字符串地址；由DS:SI 寄存器给出；使用时，需要在 END 前面声明 DEFINE_PRINT_STRING

PTHIS ; 在当前光标位置输出一个字符串（同 PRINT_STRING一样，不同之处在于它是从堆栈接收字符串。字符串终止符应在 call 之后定义；例如:
CALL PTHIS;  
db 'Hello World!', 0
; 使用时，需要在 END 前面声明 DEFINE_PTHIS  

GET_STRING ; 从用户输入得到一个字符串，输入的字符串写入 DS:DI 指出的缓冲，缓冲区的大小由 DX 设置；回车作为输入结束；使用时，需要在 END 前面声明 DEFINE_GET_STRING 

CLEAR_SCREEN ; 清屏过程(滚过整个屏幕)，然后将光标设置在左上角。使用时，需要在 END 前面声明DEFINE_CLEAR_SCREEN
　
SCAN_NUM ; 取得用户从键盘输入的多位有符号数，并将输入存放在 CX 寄存器；使用时，需要在 END 前面声明 DEFINE_SCAN_NUM

PRINT_NUM ; 输出 AX 寄存器中的有符号数；使用时，需要在 END 前面声明 DEFINE_PRINT_NUM以及 DEFINE_PRINT_NUM_UNS.

PRINT_NUM_UNS ; 输出 AX 寄存器中的无符号数；使用时，需要在 END 前面声明 DEFINE_PRINT_NUM_UNS.
```

使用例：

```assembly
include 'emu8086.inc'

ORG 100h

LEA SI, msg1 ; 要求输入数字
CALL print_string ;
CALL scan_num ; 读取数字放入 cx

MOV AX, CX ; CX 存放数值拷贝到 AX

; 输入如下字符
CALL pthis
DB 13, 10, 'You have entered: ', 0

CALL print_num ; 输出 AX 中的字符

RET ; 返回操作系统

msg1 DB 'Enter the number: ', 0

DEFINE_SCAN_NUM      ; 使用这些宏需要添加的
DEFINE_PRINT_STRING
DEFINE_PRINT_NUM
DEFINE_PRINT_NUM_UNS 
DEFINE_PTHIS

END ; 结束
```







## ※ 8086 缺省 16 位段 + 偏移寻址组合表

注意：当 BX/BP 与 SI/DI 组合作为偏移地址时，默认的段寄存器是 BX/BP 所在段的寄存器，而不是 SI/DI 所在段的寄存器；

一般程序中 **只给出偏移地址**，因此需要背这张表的默认对应关系，记 **偏移寄存器 -> 段寄存器** 方向；

| 段寄存器  | 偏移寄存器            | 主要用途   |
| --------- | --------------------- | ---------- |
| CS 代码段 | IP                    | 指令寻址   |
| DS 数据段 | BX, DI, SI 或 16 位数 | 数据寻址   |
| SS 堆栈段 | SP 或 BP              | 堆栈寻址   |
| ES 附加段 | DI                    | 目标串寻址 |



## 3. 寻址方式

操作数寻址：获取操作数

指令寻址：控制指令跳转

### 3.1 操作数寻址

#### 8086 汇编指令格式

指令 = 操作码 + 操作数；

<img src=".\media\8086汇编指令格式.webp" style="zoom:38%;" />

寻址方式：为取得操作数或指令地址所使用的方法

操作数寻址：与数据有关的寻址方式

指令寻址：与转移地址有关的寻址方式  

指令寻址分为顺序寻址（IP 一条指令接一条指令地顺序进行移动）和跳转寻址（下条指令的地址码不是由程序计数器给出，而是由本条指令给出）



#### 3 种数据寻址方式

##### 立即数寻址

例：**MOV AX, 1100H**

<img src=".\media\立即数寻址.webp" style="zoom:38%;" />

- 操作数直接存在 **指令** 中，即 **立即数**（Immediate），用常量形式直接表达；
- 因此操作数存储在 **代码段（CS）**中；
- 注意：
  - 源操作数和目的操作数的 **字长必须一致**；例如 **MOV  AH, 3064H** 就是非法的；
  - 只能用于源操作数；



##### 寄存器寻址

例：**MOV  AX,  BX**

- 操作数在 CPU 的 **内部寄存器** 中；
- 不需要访问存储器，速度快；
- 注意：
  - 源操作数和目的操作数的 **字长必须一致**；例如 **MOV  AH (16 位), BX (8 位)** 是非法的；
  - 代码段寄存器 CS 不能用 MOV 指令改变（其他段寄存器都允许重新分配）；



##### 存储器寻址

- 存储器寻址共有 5 种寻址方式（见后文）
- 操作数在 **存储器** 中，用存储单元的地址表示；
- 编程时使用包含 **段寄存器** 和 **偏移地址** 的逻辑地址（一般只给出偏移地址）；
- 段寄存器指示段的 **基地址**；
- 偏移地址由各种寻址方式计算，常被称为 **有效地址 EA**（Effective Address）；
- 一般情况下存储器地址由有效地址表示，段寄存器不用显式说明，数据在默认的段中；如果不使用默认段寄存器，则要用 **段超越指令前缀** 加以说明；



#### 段寄存器的默认和超越

| 访问存储器的方式   | 默认      | 偏移地址    | 可超越             |
| ------------------ | --------- | ----------- | ------------------ |
| 取指令             | 代码段 CS | IP          | 无                 |
| 堆栈操作           | 堆栈段 SS | SP          | 无                 |
| 一般数据访问       | 数据段 DS | 有效地址 EA | CS, ES, SS, FS, GS |
| BP 基址的寻址方式  | 堆栈段 SS | 有效地址 EA | CS, ES, SS, FS, GS |
| 串操作的源操作数   | 数据段 DS | SI          | CS, ES, SS, FS, GS |
| 串操作的目的操作数 | 附加段 ES | DI          | 无                 |



#### 5 种存储器寻址方式

做题注意一下哪边是高地址，哪边是低地址；

##### 直接寻址

例：**MOV CX，[2000H]**

- 指令中直接包含了操作数的有效地址 EA，在指令操作码之后；
- 默认段地址在DS寄存器中，即操作数的实际地址（物理地址）是 DS:EA ；
- 常用于存取变量；



##### 寄存器间接寻址

例： **MOV AX，[SI]**

- 操作数的有效地址 EA 存放在基址寄存器 BX 或变址寄存器 SI, DI 中，**不能放在 AX / CX / DX 中**；
- 操作数的段地址（数据处于哪个段）取决于 **选择哪一个间址寄存器**；
- 可以方便地对数组的元素或字符串的字符进行操作；
- 寄存器间接寻址没有说明存储单元类型；



##### 寄存器相对寻址

例： **MOV BX，NUM+[DI]** 

- 有效地址 EA 是 **寄存器内容与位移量之和**；
- 适用于数组、字符串、表格的操作；

<img src=".\media\寄存器相对寻址.webp" style="zoom:38%;" />



例：计算寄存器相对寻址的有效地址

<img src=".\media\寄存器相对寻址例题.webp" style="zoom:38%;" />



##### 基址变址寻址

例：**MOV AX，\[BX][SI]**

- 有效地址是基址寄存器和变址寄存器之和
- 适用于二维数组、字符串、表格的操作
- 必须是一个基址寄存器和一个变址寄存器的组合
  - **MOV  AX,  \[BX][BP]**    非法，这是两个基址寄存器
  - **MOV  AX,  \[SI][DI]**      非法，这是两个变址寄存器

<img src=".\media\基址变址寻址.webp" style="zoom:48%;" />



##### 相对基址变址寻址

例：**MOV AX，MASK\[BX][SI]**

也可以写成 **MOV AX, [BX+SI+MASK]** 

或 **MOV AX, MASK[BX+SI]**

- 有效地址计算如下：

<img src=".\media\相对基址变址寻址.webp" style="zoom:38%;" />



例：计算相对基址变址寻址的有效地址

<img src=".\media\相对基址变址寻址例题.webp" style="zoom:38%;" />



#### 操作数寻址总结

1. 立即数寻址                MOV  AX , 3069H
2. 寄存器寻址                MOV  AL , BH
3. 直接寻址                    MOV  AX , [2000H]
4. 寄存器间接寻址         MOV  AX , [BX] 
5. 寄存器相对寻址         MOV  AX , NUM [ SI ] 
6. 基址变址寻址             MOV  AX , \[BP][DI]
7. 相对基址变址寻址      MOV  AX , MASK \[BX][SI]



#### 未解决的疑问

2021/9/13：老师说下次课讲

2021/9/16：讲了，但没完全讲.jpg

作业题：假设（DS）= 2000H，（ES）= 2100H，（SS）= 1500H，（SI）= 00A0H，（BX）= 0100H，（BP）= 0010H，数据段中的变量名VAL的**偏移地址值**为 0050H；试指出下列源操作数的寻址方式是什么？其物理地址值是什么？（8）MOV  AX，VAL

（这里先放一下 lys 大佬和老师的说法：）

<img src=".\media\image-20210913193654164.webp" alt="image-20210913193654164" style="zoom:33%;" />

<img src=".\media\image-20210913193830411.webp" alt="image-20210913193830411" style="zoom:33%;" />



### 3.2 指令寻址

#### 段内直接寻址

例： 

**JMP  NEAR PTR  NEXT**    近转移    <u>-32768 ~ +32767</u>，汇编时，位移量为16位

**JMP  SHORT  NEXT**          短转移    <u>-128 ~ +127</u>，汇编时，位移量为8位

- 段内寻址 CS 不变，只改变 IP；
- 直接体现地址信息，需要进行一个转换；
- 转向的有效地址 = 当前 (IP) + 位移量 (8bit/16bit) ，其中位移量是 **转向的有效地址与当前 IP 值之差**；



#### 段内间接寻址

<img src=".\media\段内间接寻址.webp" style="zoom:48%;" />

- 段内寻址 CS 不变，只改变 IP；
- PTR 是强制类型转换；
- 转向的有效地址是一个寄存器或存储单元的内容；
- 可用除立即数以外的任何一种数据寻址方式得到；



#### 段间寻址

例：

**JMP  1234H:5678H**                        ;  (CS) = 1234H ，(IP) = 5678H

**JMP dword ptr 内存地址单元**      ;  (CS) = (内存地址单元+2) ，(IP) = (内存地址单元) 

- 段间寻址，既改变 IP 也改变 CS 转向的有效地址是 XXXX:YYYY 形式

- 一般仅供操作系统（OS）使用

  

### 3.3 伪指令

汇编语言语句分为 **指令** 和 **伪指令** 两种类型：

| 指令性语句                                                   | 指示性语句（伪指令语句）                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 能够被直接翻译成机器码，并让CPU直接执行的语句；<br>每一条指令语句对应一条可执行的CPU机器指令，执行产生相应的CPU动作；<br>有操作码和操作数两个部分； | 指导汇编程序如何编译汇编语言源程序，并不翻译成机器代码，CPU也不执行；<br>通常完成储存模式定义、数据定义、存储器分配、指示程序开始结束等功能；<br>可以提高编程效率，指导编译器如何编译汇编程序； |

<img src=".\media\汇编语言语句类型.png" style="zoom:75%;" />



#### 汇编语言语句格式 

名字定义满足的规则：

1. 数字不能作为第一个字符；
2. 单独的问号（?）不能作为名字；
3. 最大有效长度为 31；
4. 保留字不能作为名字使用；

<img src=".\media\汇编语言语句格式.png" style="zoom: 25%;" />



##### 操作码

- 含义：指明操作的性质或功能；
- 书写规则：操作码与操作数之间用空格分开；



##### 操作数

- 含义：指定参与操作的数据或者地址表达式；
- 个数：一般指令，1个或2个，也可以没有；伪指令和宏指令可以有多个；
- 书写规则：操作数多于1个时，操作数之间用逗号分开



#### 段定义伪操作

- 程序由4个逻辑段组成：数据段、堆栈段、附加段和代码段；
- 每个逻辑段以 `SEGMENT` 语句开始，以 `ENDS` 语句结束；

##### 段定义的格式

```assembly
段名  SEGMENT  [定位类型]  [组合类型]  [使用类型]  [‘类别’]	; 方括号这几项一般采取默认设置，缺省：
          ……
          ……            ; 语句序列
段名  ENDS
```

例：

```assembly
DATA   SEGMENT         ; 定义数据段
       …
DATA   ENDS
;----------------------------------------
EXTRA  SEGMENT         ; 定义附加段
       …
EXTRA  ENDS
;----------------------------------------
CODE   SEGMENT         ; 定义代码段
       ASSUME CS:CODE, DS:DATA, ES:EXTRA
START: 
       MOV   AX, DATA
       MOV   DS, AX    ; 段地址 -> 段寄存器
       …
CODE   ENDS
       END   START
```



#### 指定段地址伪指令

- 伪指令的格式：**ASSUME <段寄存器名>:<段名>[,<段寄存器名>:<段名>…]**
- 功能：建立段寄存器与段的缺省关系；
- 注意：ASSUME 伪指令 **是给程序员看的**，并不为段寄存器设定初值，没有真正建立关联；



**例： assume cs:code, ds:data, es:extra**

其中：code 是代码段的段名，data 是数据段的段名，extra 是扩展段的段名



#### 设置段地址值

- 在代码段开始处进行 DS, SS, ES 的段地址装填；
- DS, SS, ES 默认指向 PSP，CS 默认指向 PSP 完成段后的地址；

<img src=".\media\设置段地址值.png" style="zoom:38%;" />

* 8086 **不支持将数据直接送入段寄存器** ，DATA 是地址常量，只能通过 AX 中继一下才能进入 DS 
* MOV 也不能在段寄存器之间直接赋值；



#### 程序结束伪操作

- 格式：**END  [ label ]**

例：

```assembly
code   segment           ; 定义代码段
       assume cs:code, ds:data, es:extra
start: 
       mov   ax, data
       mov   ds, ax      ; 段地址 -> 段寄存器
       …
code   ends
       end   start
```



#### 数据定义及存储器分配伪操作

- 格式：**[变量] 助记符 操作数 \[,操作数,…] \[;注释]** 

- 助记符：**DB, DW, DD, DQ (8字节), DT (10字节)** 

  - DB： 定义字节
  - DW：定义字
  - DD： 定义双字

  

<img src=".\media\数据定义以及储存器分配伪操作.png" style="zoom:48%;" />

例：

```assembly
DATA_BYTE  DB  10,4,10H,?            
DATA_WORD  DW  100,100H,-5,? 	; -5 按补码存储
```

<img src=".\media\数据定义例.png" style="zoom:38%;" />

这里 **DB 2 DUP (0,2 DUP(1,2),3) = 2 DUP（0, 1, 2, 1, 2, 3）= （0, 1, 2, 1, 2, 3, 0, 1, 2, 1, 2, 3）**

<img src=".\media\数据定义例2.png" style="zoom:38%;" />



##### 操作数

<img src=".\media.\操作数.png" style="zoom: 50%;" />

**MOV AL, OPER2**  和 **MOV AL, [OPER2]** 是等价的；  

<img src=".\media\操作数和类型匹配.png" style="zoom:38%;" />



#### 表达式赋值伪操作

- 相当于给表达式起一个别名，碰到别名就替换成表达式；
- 格式：表达式名 EQU 表达式
- 同一个程序中 `=` 可以对一个符号重复定义，但 EQU 不能对同一个符号重复定义
- 注意：EQU 指令不占用内存空间；

```assembly
BUF1 DW 1,2,3
INTT EQU 5
BUF2 DW 4,5,6       ; 3和4的地址是连续的
```



#### 解除定义伪指令

- 格式：PURGE <符号1，符号2，…，符号n>
- 功能：解除指定符号的定义



#### 地址计数器与对准伪操作

- 常用于确定数组中元素的个数

```assembly
BUF1 DB 1，2，3，4，5
CNT1 EQU $-BUF1              ;（常用）
BUF2 DW 1，2，3，4，5
CNT2 EQU ($-BUF2)/2          ;（/2后等于数组元素个数）
```

- 在代码段，\$ 表示当前正在汇编的指令的地址；

```assembly
ORG   $+8       ;  跳过8个字节的存储区
JNE   $+6       ;  转向地址是 JNE 的地址 +6
JMP   $+2       ;  转向下一条指令
```

- 在数据段，\$ 表示当前地址计数器的值；

```assembly
ARRAY   DW 1, 2 , $+4 , 3 , 4 , $+4
```

结果如图：

<img src=".\media\地址计数器内存图.png" style="zoom:38%;" />



#### 表达式操作符

在指令中出现，主要是对常量进行运算

| 运算符类型 | 运算符及说明                                          |
| ---------- | ----------------------------------------------------- |
| 算术运算符 | + 、－、*、 /、MOD（取余数）                          |
| 逻辑运算符 | AND（与）、OR（或）、XOR（异或）、NOT（非）、SHL、SHR |
| 位移运算符 |                                                       |
| 关系运算符 |                                                       |

<img src=".\media\运算符及说明.png" style="zoom:48%;" />



##### 算术运算符

```assembly
VIDEO_BUF  DB  25*80*2  DUP(?)
ARRAY   DW   1,2,3,4,5,6,7
ARYEND  DW   ?
MOV  CX, (ARYEND-ARRAY)/2	; 赋值
BLOCK DB  1，2，3

MOV  AL, BLOCK+2   ; 符号地址 ± 常数有意义，等价于  MOV  AL, [BLOCK+2] 
```



##### 逻辑和移位操作符

```assembly
OPR1  EQU  25    ; 00011001B
OPR2  EQU  7     ; 00000111B

AND  AX, OPR1 AND OPR2     ; AND AX,1

MOV  AX,  0FFFFH SHL 2     ; MOV AX,0FFFCH
```



##### 关系操作符 

```assembly
MOV FID, (OFFSET Y - OFFSET X) LE 128
; 若 <= 128  (真)   汇编结果：  MOV  FID, -1
; 若 >  128  (假)   汇编结果：  MOV  FID, 0
```



##### 数值回送操作符

OFFSET、SEG、LENGTH、SIZE

- **OFFSET /  SEG**      变量 / 标号
  功能：回送变量或标号的偏址 / 段址
  如 **MOV DX, SEG FUNC**

- **LENGTH**   变量
  功能：回送由DUP定义的变量的单元数，其它情况回送1

- **SIZE**   变量
  功能：返回配送给该变量的字节数
  等价于 **LENGTH * TYPE        ;DB的TYPE值为1，DW的TYPE值为2**



例：

```assembly
ARRAY   DW   100 DUP (?)
TABLE    DB   ‘ABCD’

MOV  CX,  LENGTH  ARRAY   ;  MOV  CX, 100
MOV  CX,  LENGTH  TABLE   ;  MOV  CX, 1
MOV  CX,  SIZE    ARRAY   ;  MOV  CX, 200
MOV  CX,  SIZE    TABLE   ;  MOV  CX, 1
```



##### 属性修改的伪指令： PTR

- **BYTE PTR**     表示字节，**WORD PTR**  表示字（2个字节）
  - 例：**BYTE PTR [BX]       ; 按字节访问 BX**
  - ​        **WORD PTR [BX]    ; 按字访问 BX** 
- 格式： 新属性 PTR （旧属性的）表达式
- 功能：用于 **暂时改变** 内存变量或标号的原有属性，但不能修改寄存器的原有属性
- 方式：与其它汇编指令配合使用，不能独立使用

例：**MOV  AX, WORD PTR [BX]**

应用场合：

```assembly
; 1. 有一些指令中，操作数或表达式的属性是不明显的，需要加以明确。
CALL DWORD PTR [BX]    ; 远调用
CALL BYTE PTR [BX]     ; 段内近调用

; 2. 需要修改操作数或表达式的属性的。
F1 DW 1234H
MOV AL，BYTE PTR F1 ；AL=34H
F2 DB 23H，56H，18H
MOV BX，WORD PTR F2 ；BX=5623H
```



##### 定位类型的伪指令： PARA

- 功能：PARA (Paragraph,节) 表明该段起始地址对齐到 para ，供 OS 使用
- 一般各个逻辑段的首地址在‘节’的整数边界上（每 16 个存储单元叫做一节, 1 para = 16 Bytes），即每个逻辑段的起始地址是 16 的整数倍，或地址的最低 4 位为 0


例： **code segment para ‘code’**

