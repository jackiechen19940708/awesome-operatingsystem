# 计算机系统漫游
## 计算机的硬件组成
1. CPU
2. Main Memory
3. Hard Drive
4. IO
5. Bus
## 程序的存储与执行
## 程序语言的设计和进化
## 存储设备的层级结构
## 操作系统
# RISC-V ISA
## ISA
isa instruction set architecture，指令集架构，是底层硬件电路面向上层软件程序提供的一层接口规范，它定义了
1. 基本数据类型（BYTE、HALFWORD、WORD）
2. 寄存器
3. 指令
4. 寻址模式
5. 异常或者中断的处理方式
## CISC vs RISC
1. CISC 复杂指令集。针对特定功能实现特定的指令，导致指令数目比较多，但是生成的程序长度相对较短
2. RISC 精简指令集。只定义常用指令，对复杂的功能采用常用的指令组合实现，导致指令数目比较精简，生成的程序长度相对较长。
## ISA宽度
isa宽度指的是CPU中通用寄存器宽度，决定了寻址范围大小以及数据运算能力
## RISC-V
一个免费、开源的指令集：
1. 简单
2. 清晰的分层设计
3. 模块化

x86是增量ISA，下一代处理器不仅实现新的ISA拓展，还必须实现过去的所有拓展，目的是保持向后的二进制兼容性。2014年 x86 已经有了1338个指令，而1978年才80个

RISCV是模块化ISA：由1个基本整数指令集+多个可选的拓展指令集组成。基础指令集是固定的，永远不会改变的

RV\[###]\[abc...xyz] 

\[###]: 处理器字宽

\[abc...xyz]: 处理器模块集合
I 整数指令集，必须实现

M 整数乘法指令集

A 存储器原子指令集

F 单精度浮点指令集

D 双精度浮点指令集

G IMAFD

C 压缩指令集

### GPR 通用寄存器

32个通用整数寄存器，FD拓展需要32个浮点寄存器

寄存器宽度由ISA指定

各个寄存器编程时特定用途&别名由RISCV ABI决定

### HART
hardware thread,虚拟CPU，类似超线程概念， 指令执行流 ， 一个cpu可以执行两个指令执行流；
software running in a given execution environment, a hart is a resource that autonomously fetches and executes RISC-V instructions within that environment

### 特权级别
User/Application 操作系统用户态

Supervisor 操作系统内核态（支持虚拟地址）

Machine 机器态（物理地址）

有指令可以切换状态

### CSR
control and status register
不同特权级别下分别对应各自的一套Register，用于控制和获取相应level下的处理器工作状态

高级别的特权级别下可以访问低级别的CSR，比如Machine Level下可以访问Supervisor、User Level的CSR；反之不可以

### 内存管理与保护
1. 物理内存保护（Physical Memory 
Protection PMP）
允许M模式指定U模式可以访问的内存地址
支持RWX & lock
2. 虚拟内存（Virtual Memory）
需要支持Supervisor Level
可以实现高级操作系统特性
多种映射方式
4. 稳定
5. 社区化

## 异常&中断
1.异常
指令不合法（读了不合理的内存位置、写了只读内存位置），CPU会知道发生了异常，然后就会跳转到一个指令地址执行异常处理（通常是你自己写的）,处理完成后，会回到**发生异常的指令**，再次执行这条指令，给你一个改过自新的机会

2.中断
执行某条指令的时候，一个中断（外部）发生了，会执行中断处理，会跳到**发生中断指令的下一条指令**。
# 编译与链接
# 嵌入式开发介绍
# RISC-v 汇编语言编程
RISC-V ISA 介绍+例子看 [wangchen](https://www.bilibili.com/video/BV1Q5411w7z5?p=7&vd_source=d8b32fb6f52ab34863975f2f585cf269)
# RVOS 介绍
操作系统是一组系统软件程序

主管并控制计算机操作、运用和运行硬件、软件资源

提供公共服务来组织用户交互

狭义：内核

广义：发行包+一组软件
# Hello RVOS
## 系统引导
每个外设都有一个物理地址与其对应
ROM 0x000 0xf000 （掉电不丢失，固化在芯片里） Bootloader
RAM 0x8000 128M 
硬件首先跑到ROM地址位置，执行Bootloader，跳转到RAM kernel上 (真实硬件除了跳转还要做其他，比如初始化设备，把kernel从硬盘搬到RAM相应位置)

## bootloader
1. 判断第一个HART，使用CSR instruction访问CSR 

2. 初始化栈

3. 跳到c函数

## UART
universal asynchronous receiver and transmitter
串行，一位一位的发送和接受；波特率就是每秒传输的二进制位数，单位bps
异步，不需要时钟线，直接发送数据，但是要约定通讯协议格式（标记开始&结束）
全双工，同事进行收发两方向的数据传输

UART通讯协议
空闲位：高点平1
起始位：先发送一个低电平0，表明传输开始
数据位：待传输数据，一般8个bit，先低位，再高位
奇偶校验：
（1）无校验
（2）奇偶校验
（3）mark parity：校验位始终为1
（4）space parity：校验位始终为0
停止位：1、1.5、2位高电平1


### UART编程
查看UART编程手册
寄存器映射内存位置 0x10000000
volatile用法，易变的，编译器不要自作主张优化
初始化UART配置：传输word长度、stop bits长度、波特率、校验等等
写数据(轮询处理方式、中断处理方式)

# 内存管理
## link script链接脚本
ld有一套默认脚本，用于链接时指定相关操作 后续详细学习；看UCB cs61,例子可以看wangchen老师代码

ENTRY： 程序入口

MEMORY： 内存位置

SECTION：段操作
告诉链接器如何将input sections映射到output sections，以及如何将output sections放置在内存中

PROVIDE()赋值命令

OUTPUT_ARCH：大致的架构，具体是32 64位后续编译选项决定

## page内存分配
已知链接器定义的heap位置后，可以开始进行内存分配了

malloc是在page级别的分配后，进行字节级别的分配

# 上下文切换和协作式多任务
## 多任务与上下文
多任务： 多个指令执行流 线程

上下文：任务执行依赖的东西（寄存器->指定了栈的位置）


## 协作式多任务的设计与实现
Cooperative Multitasking:协作式多任务环境下，下一个任务被调度的前提是当前任务主动放弃处理器

Preemptive Multitasking：抢占式多任务环境下，操作系统完全决定任务调度方案，操作系统可以剥夺当前任务对处理器的使用，将处理器提供给其它任务。

switch_to
1.保存之前的上下文
2.切换任务
3.加载新任务的上下文
4.ret
# Trap和Exception
## 控制流 control flow 和Trap
控制流：branch jump=》正常控制流

异常控制流（Exceptional Control Flow ECP）：exception & interrupt

ECP=》Trap


## Trap 涉及的寄存器
Machine模式下CSR列表里有
Machine Trap Setup
Machine Trap Handling

mtvec:trap发生时将要跳到的处理函数位置（ direct mode ，vector mode）

mepc：保存发生trap的pc位置

mret：恢复到mepc指向的地址

mcause：导致trap发生的原因；最高位为1时表示为interrupt，否则为exception；剩余表示具体的interrupt或者exception种类

mtval:辅助mcause提供更详细的trap信息，具体的trap信息是由特定的硬件实现定义。RISC-V没有定义具体的值，但定义了一些行为，比如访问地址出错时地址信息，或者执行非法指令时的指令本身等

mstatus：描述状态信息
xIE（MSU）：1打开全局中断，0关闭全局中断
xPIE（MSU）：trap发生时，保存trap发生前的xIE
xPP（MS）：trap发生时，保存发生之前的权限值


## Trap 处理流程
1. trap初始化
设置trap寄存器，比如bottom half的处理函数地址
2. Trap的Top Half
硬件处理
(1) 把mstatus的MIE复制到MPIE中，清楚mstatus中的MIE标志位=》中断被禁止，不会被打断
(2) 设置mepc，同事PC被设置为mtvec。对于exception，mepc指向导致异常的指令；对于interrupt，mepc指向被中断的指令的下一条指令的位置
(3) 根据trap的种类设置mcause，并根据需要位mtval设置附加信息
(4) 将trap发生之前的权限保存在mstatus的MPP中，再把hart权限模式更改为M（也就是说无论在任何level下触发trap，hart首先切换到Machine模式）
3. Trap的Bottom Half
软件部分
（1） 保存当前控制流的上下文信息
（2） 调用C语言的trap handler
（3） 从trap handler函数返回，mpec的值可能需要调整
（4） 恢复上下文的信息
4. 从Trap返回
执行mret指令返回trap之前的状态
比如mret， 恢复权限、MIE MPIE pc等
# 外部设备中断
## 中断分类
1. 本地中断
software interrupt
timer interrupt
2. 全局中断
external interrupt

每类都都有User Supervisor Machine中断

三类中断对Hart有三个引脚

## 中断寄存器
除了exception寄存器外，还有

mie（Machine Interrupt Enable）：打开（1）或者关闭（0）MSU模式下对应的External Timer Software中断

mip(Machine Interrupt Pending):获取当前MSU模式下对应的External Timer Software中断是否发生o

## 中断发生时Hart自动执行如下状态转换
(1) 把mstatus的MIE复制到MPIE中，清楚mstatus中的MIE标志位=》中断被禁止，不会被打断
(2) 设置mepc，同事PC被设置为mtvec。对于exception，mepc指向导致异常的指令；对于interrupt，mepc指向被中断的指令的下一条指令的位置
(3) 根据trap的种类设置mcause，并根据需要位mtval设置附加信息
(4) 将trap发生之前的权限保存在mstatus的MPP中，再把hart权限模式更改为M（也就是说无论在任何level下触发trap，hart首先切换到Machine模式）

## 外部中断
外设通过总线，中断通知CPU

PLIC Platform Level Interrupt Controller

PLIC接受多种外设（中断源头，每个都有一个ID），然后接到每一个Hart External Interrupt引脚；像是集线器

PLIC编程接口寄存器
Priority 优先级寄存器，地址为BASE+interrupt-id*4

Pending，用于表示某一路中断源是否发生，地址为BASE+0x1000+(interrupt-id)/32=》1个bit表示一个外设

Enable,针对某个hart开启或者关闭某一路中断源，地址为。。。o

Threashold，针对某个hart设置中断源优先级的阈值，特别的阈值为0允许所有中断源发生中断；为7表示禁止所有中断源发生中断。

Claim/Complete:同一个寄存器，每个Hart一个，针对该寄存器读操作称之为Claim，即获取当前发生的最高优先级的中断源ID。Claim成功后悔清楚对应的Pending位。 对该寄存器执行写操作称之为Complete。通知PLIC对该路中断的处理已经结束。

# 硬件定时器
CLINE （Core Local INTerruptor），负责产生软件中断和定时器中断
CLINT寄存器也采用内存映射方式

## CLINT 寄存器
mtime real-time计数器，地址略；系统全局唯一，时64-bit，系统必须保证该计数器的值始终按照一个固定的频率递增。上电复位时，硬件负责将mtime的值恢复为0.

mtimecmp timer compare register。每个hart一个mtimecmp寄存器，是64bit，上电复位时系统不负责设置mtimecmp的初值。

当mtime>=mtimecmp时，CLINT会产生一个timer中断。如果要使能该中断需要保证全局中断打开且mie.MTIE标志位为1.当timer中断发生时，hart会设置mip.MTIP,程序可以在mtimecmp中写入新的值清楚mip.MTIP。**中断只会发生一次，后面需要重新设置**！

# 抢占式多任务
操作系统完全决定任务调度方案，操作系统可以剥夺当前任务对处理器的使用，将处理器提供给其他任务。
通过时钟中断&软中断实现
# 任务同步和锁
## 并发
多个控制流同时执行，内存是一个：
多处理器多任务
单处理器多任务
单处理器单任务+中断


## 同步
保证并发执行环境中，各个控制流可以正常执行

## 临界区
Critical Section，临界区，指的是一个会访问共享资源（共享设备、共享内存等）的指令片段，而且这样的多个指令片段同时访问某个共享资源时可能会引发问题。

## 锁
锁来实现只有一个控制流能进入临界区

不可睡眠的锁 任务不可以离开调度队列
可睡眠的锁 任务可以离开调度队列


## 死锁
多把锁，互相想要对方

## 自旋锁
不可睡眠的锁，会参与调度

spin lock=》自己空转，占用资源

一种实现=》关闭中断


# 软件定时器
硬件定时器——外部晶振提供，提供寄存器设置超时时间，外部方式通知cpu；精度高，但是定时器个数受硬件芯片的设计限制

软件定时器，基于硬件定时器功能，采用软件方式实现，可以提供很多定时器，但是精度较低，必须是Tick的整数倍o

## 软件定时器分类
单次触发定时器：创建后只会触发一次定时器通知事件，出发后该定时器自动停止（硬件就是单次的）

周期触发定时器：创建后按照周期无线循环触发定时器通知事件，直到用户手动停止，

超时函数运行在中断上下文环境中：执行函数的执行时间尽可能短，不可以执行等待其他事件等可能导致中断控制路径挂起的操作。优点是响应比较迅速，实时性较高。

超时函数运行在任务上下文环境中：创建一个任务来执行这个函数，函数中科院等待或者挂起，单实时性较差。

本质上是利用硬中断+一些超时来实现软中断

# 系统调用
## 用户态和内核态
用户态 User 模式

内核态 Machine 模式

mret会根据MPP[1:0] 和 MPIE 分别设置return后的模式和中断使能

## 切换用户态 内核态
系统调用：封装函数，从用户态跳到内核态，进行相关资源操作

ecall：主动触发异常，进入machine状态进行操作,根据调用ecall的权限级别产生不同的exception code，异常产生时epc寄存器的值存放的是ecall指令本身的地址。

## 系统调用执行流程

用户态调用ecall->trap_handler->调用系统函数->mret；有一个经典图，可以看wangchen老师ppt

 
## 系统调用传参
系统调用号(多个系统调用，通过一个编号来区分，这个编号就是系统调用号) 放在a7

传参使用a0->a5

返回使用a0 


完成后eret回到user态
