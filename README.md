# 关于RISC-V你所需要知道的一切

当人们在谈论RISC-V的时候，实际在谈些什么？本书尝试告诉您答案。

本书计划采用众包的方式完成，欢迎RISC-V的爱好者贡献自己的力量，以推动RISC-V在中国的普及，同时共同学习进步。

[![](/assets/import.png)](https://creativecommons.org/licenses/by-nc-sa/3.0/cn/)

本作品采用[知识共享署名-非商业性使用-相同方式共享 3.0 中国大陆许可协议](https://creativecommons.org/licenses/by-nc-sa/3.0/cn/)进行许可。

**待讨论大纲（以下是囿于我的见识列出的大纲草稿，有很多不完善地方，大家多讨论）：**

# 第一章 RISC-V产生的时代背景

## 1 计算机体系结构和处理器微结构

- ISA的产生

- 区分计算机体系结构和微结构
  - 体系结构(computer architecture)定义的是硬件和软件的接口，并没有指定实现。RISC-V即定义的体系结构。
  - 微结构(processor microarchitecture)则描述的是如何设计一个处理器来符合一个体系结构。体系结构并不定义微结构。

- 指令级架构(instruction set architecture, ISA)
  - ISA定义的内容：指令编码，内存模型，IO模型，逻辑寄存器数量和功能，控制寄存器，特权级别。
  - 一条指令都包含什么： 指令功能，操作对象(立即数，普通寄存器，特殊寄存器，内存), 环境变量(标志位，特权级别)，指令编码。
  - 指令的功能分类：逻辑指令，数学指令，控制指令，内存指令，IO指令，特殊指令。
  - CISC和RISC
- 处理器微结构
  - 流水线
  - 多发乱序流水线
  - 多线程流水线
  - 多核
  - 特殊流水线：VLWI, vector, GPU
- 体系结构与微结构的互相影响
  - 比较CISC和RISC的特性。
  RISC的每条指令功能复杂度基本一致，执行时间基本一致，编码长度基本一致，流水线控制简单，指令调度简单，代码密度相对CISC较小。
  CISC的指令功能复杂度不一，执行时间长短不一，编码长度也不一样。直接运行导致流水线控制复杂调度困难，一般动态拆做类似RISC的微指令执行。代码密度相对RISC较高。
  - 操作数：单指令寄存器个数。从栈寻址到多寄存器寻址在代码密度，流水线调度，执行时间上的考虑。
  - 操作数：内存寻址方式。RISC的内存寻址方式单一，调度简单，可做单独流水线，但指令密度高。CISC调度方式复杂，流水线设计复杂，指令密度高。
  - 环境变量： 条件执行对流水线的影响。（第二章具体讲）
  - 指令编码：RISC编码和SISC编码，解码难度，取址难度，分支预测难度等等。（第二章具体讲RISC-V的编码设计）
  - 编译器：
    编译器是指令集和高级语言的接口。
    语言的抽象等级越高，ISA的使用就更加受限：ABI，systemcall，context switching。
    通用逻辑寄存器的功能异化，数量对编译器的影响。
  - 操作系统：硬件资源的管理者，特权软件。
    操作系统需要ISA的支持：控制寄存器，特权指令，内存模型，IO模型等等，hyervisor模式。
    （更具体的，比如对VM，安全，上下文切换效率，中断和异常的定义等等，都留到第二章）

## 2 现有指令集 (leishangwen)
- CISC指令集的代表：x86和x86-64。
- RISC指令集的代表：ARM(ARMv7,AArch32,AArch64,thumb, thumb2)
- 其他的指令集：MIPS, PowerPC, SPARC

## 3 硬件开发的变迁 (wsong83)
介绍从最开始的晶体管，到CMOS，到基于标准单元的版定制流程，自动综合和布局布线，物理综合，仿真，前仿和后仿，LVS和formal verification，最终到SystemVerilog的verification特性和HLS的出现。这张的目的是给不理解硬件设计的读者入个门。后面讲到Rocket的某些硬件优化的时候会有好处。

## 4 开源运动
这有三个方面：开源软件，发生的原因和意义，其优势和现在的广泛使用（Linux，GNU GCC等等）
现存的问题：License的斗争，开发缓慢的问题，分支严重的问题，patent的问题，难以商业化不挣钱的问题。

开源硬件，早已出现，OpenRISC和OpenCore社区，但是为何不太成功：开发人员少群众基础不够，项目多数未完成，完成的也缺少验证，难以流片验证，缺少开源工具链，模拟设计难以开源，开源设计难以流片的各种实际因素。当然也说现在已有的成果，开源模拟器，简单的开源逻辑综合器，开源的硬件设计等等。

指令及开源：指令集本身对性能影响其实不大，但是一个指令集所附带的生态环境非常重要。选用一个指令集其实是选择了一个生态环境，为这个生态环境付费。开源指令集的主要目的其实是提供一个开源的生态环境，包括ISA, 编译器，操作系统，软件库等等。

# 第二章 RISC-V

这一章就讲RISC-V指令集

## 1 RISC-V的历史
介绍UCB, MIPS, RISC-V的出现，开源，基金会的建立，它的目的和意义。

> wsong83:<br>
> - 关于UCB的历史，可以看看[RISC-V Geneology](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2016/EECS-2016-6.pdf)

## 2 RISC-V的基本设计原理
介绍RV64I和RV32I的基本情况，重点介绍设计原理，可扩展的方式，面向硬件设计的编码方式，面向简单流水线设计的指令选择（不用条件执行，不用多周期指令）。

## 3 RISC-V特权指令设计
为何选择使用CSR，CSR的设计细节，不同特权级的定义，特权级之间的跳转，delegation的使用。（这里可以说的惊人细节太多了）

## 4 内存模型
介绍什么是内存模型，virtual memory的设计，和RISC-V的内存模型（这一部分快定义好了）

## 5 RISC-V的压缩指令
为什要有压缩指令及，怎么设计的，性能数据。结合压缩指令及讨论instruction fusion。简单说说压缩指令对硬件的影响，比如指令地址unalign，fetch级的动态取指速度，节省I$等等。

## 6 RISC-V的扩展指令集

AMO和LR/SC的设计意图（原子操作和关键区支持）。M/D/F/E等等已经可以用的扩展，和计划中的扩展。

## 7 Spike

Spike是RISC-V的instruction set simulator (ISS)，也是独立于实现的标准参照。
应该讲一讲Spike的使用和其背后的原理。

## 8 RISC-V的软件生态
介绍围绕RISC-V开发的编译器、移植的操作系统等软件的情况。

## 9 RISC-V在产业界与学术界的现状（leishangwen）
概述基于RISC-V指令集的处理器、SoC，并进行简单地汇总统计。


# 第三章 Rocket-Chip概述

简单介绍Rocket-chip最初设计的由来，几次流片经历，作为RV64G的主要硬件实现，最终作为freechipsproject脱离UCB。
此处简单提及Rocket和Boom是Rocket-chip支持的两种处理器实现，Freedom和lowRISC是基于Rocket-chip的两个SoC扩展。

> 讲流片经历的时候，讲事件，不具体讲性能结果。留到第四章。

## 1 Chisel和FIRRTL

简单说明Chisel和FIRRTL的功能。简单介绍Chisel相对SystemVerilog等HDL的优势，同时区分Chisel和HLS。
举简单例子来说明：

- Scala/Chisel的基本语法(例子中会涉及的部分)。
- Chisel可以被用来实现简单电路。
- Chisel在泛型上的优势。
- Chisel在面向对象上的优势。
- Chisel用LazyModule来实现编译时代码生成机制。

## 2 Rocket-Chip的基本结构

画几个结构图来形式化的表示Rocket-chip的内部链接。同时叙述Rocket-chip的可配置功能。
同时在这里介绍devicetree的自动生成。

## 3. TileLink片上总线

TileLink总线的channel名称和功能，支持的报文类型和传输协议等等。

## 4. 缓存一致性与片上互联总线
介绍Rocket-Chip采用的缓存一致性策略、片上互联总线的结构图、多核结构等。

## 5. Rocket-chip的仿真和测试

这里主要是讲仿真的基本方法和Rocket-chip/Chisel提供的几种测试方法：require check, assertion, bus-monitor, unitest, groundtest, isa regression.
这一章并不具体讲测试和仿真的基本步骤。

# 第四章 Rocket处理器

## 1. Rocket处理器介绍
包括特点（比如：无阻塞存储等）、性能参数（已流片的芯片的性能）、流水线图、结构框图、接口图等。

## 2. Rocket处理器指令解码的过程分析
以图示的方式分析指令解码的实现。

> wsong83:<br>
> 这里我不太明白。解码过程，你指的的是instruction decoder吗？
> 其实没有太多可以讲的，说简单了，就是一段组合判断逻辑，相当于SystemVerilog的casez语句。
> 由于case很多，所以有的时候会变成瓶颈，于是分作多个周期。Rocket现在还是一个周期。
> 或者我这么问，你想知道些什么？<br>
> leishangwen:<br>
> 在2.2节介绍过RISC-V基本指令集及其优势，这里应该可以分析一个实际的处理器，更加直观、形象的解释RISC-V的优势，在译码方面的优势，不知这样理解正确与否？

## 3. Rocket处理器DCache设计分析
重点分析其中的MSHR、DCache的访问过程

## 4. Rocket处理器RoCC设计分析（leishangwen）
分析RoCC接口，并可以通过仿真实验说明RoCC如何使用


# 第五章 BOOM处理器

## 1. BOOM处理器介绍
包括特点（比如：乱序、超标量）、性能参数、结构框图、接口图等，src/main/scala目录下的文件的基本作用。

## 2. BOOM处理器的推测发射设计分析
以图示的方式分析

## 3. BOOM处理器的寄存器重命名设计分析
以图示的方式分析

## 4. BOOM处理器的数据存储过程分析
以图示的方式分析

## 5. BOOM处理器的指令提交过程分析
以图示的方式分析

# 第六章 SiFive公司的Freedom系列

## 1. Freedom系列简介（leishangwen）
介绍SiFive公司及其Freedom系列的基本情况

## 2. Freedom E310介绍
介绍Freedom E310的性能参数、结构框图、接口图、各个模块的作用、地址空间分配、启动顺序等

## 3. Freedom E310仿真实验
仿真步骤，实验环境搭建，实验步骤（包括下载到开发板的步骤），测试例程分析（以SiFive提供的Eclipse开发环境中的自带测试历程进行分析，主要是分析启动过程的代码）

## 4. Freedom E310调试过程及原理
首先是介绍如果调试，给出步骤，做实验，给出实验截图。然后，分析调试的原理，包括debug rom的内容、openocd的设置及基本工作原理、JTAG总线的知识、Freedom E310对于调试指令的处理，并进一步分析step、break、continue等调试指令的实现原理。

## 5. Freedom E310运行FreeRTOS
介绍如何使FreeRTOS在Freedom E310上的运行

# 第七章 LowRISC (wsong83)

包括LowRISC的产生原因、发展历史、相比Rocket的主要改进点，仿真步骤，实验环境搭建，实验步骤（包括下载到开发板的步骤）；
> leishangwen：<br>
> lowrisc这一部分需要宋同学多费心了，建议多讲特性、结构、内部工作机理、参数化使用等<br>
> 另外，建议结合试验或者仿真，可以更好地讲解lowrisc的一些特性，比如tagged memory等<br>
> 所以，这里的试验不是简单地试验，不是按照网站的说明，一步步操作，而是要结合内容自己设计试验<br>

# 第八章 RISCV-mini源代码分析

分析RISCV-mini或者sodo、zscala的源代码，以小见大，而且因为简单便于大家理解（lsl之前积累过sodor源代码的相关的资料）

