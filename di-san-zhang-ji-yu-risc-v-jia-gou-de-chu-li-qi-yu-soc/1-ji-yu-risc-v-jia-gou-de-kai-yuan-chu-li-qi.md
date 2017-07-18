## **3.1 基于risc-v架构的开源处理器**

### **3.1.1 标量处理器——Rocket**

Rocket是UCB设计的一款64位、5级流水线、单发射顺序执行处理器，主要特点有：

* 支持MMU，支持分页虚拟内存，所以可以移植Linux操作系统
* 具有兼容IEEE 754-2008标准的FPU
* 具有分支预测功能，具有BTB（Branch Prediction Buff）、BHT（Branch History Table）、RAS（Return Address Stack）

Rocket是采用Chisel（Constructing Hardware in an Scala Embedded Language）编写的，这也是UCB设计的一种开源的硬件编程语言，是Scala语言的领域特定应用，可以充分利用Scala的优势，将面向对象（object orientation）、函数式编程（functional programming）、类型参数化（parameterized types）、类型推断（type inference）等概念引入硬件编程语言，从而提供更加强大的硬件开发能力。Chisel除了开源之外，还有一个优势就是使用Chisel编写的硬件电路，可以通过编译得到对应的Verilog设计，还可以得到对应的C++模拟器。Rocket使用Chisel编写，就可以很容易得到对应的软件模拟器。同时，因为Chisel是面向对象的，所以Rocket的很多类可以被其他开源处理器、开源SoC直接使用。

Rocket已经被流片11次之多，其中采用台积电40nm工艺时的性能与采用同样工艺的，都是标量处理器的ARM Cortex-A5的性能对比如表3-1所示。可见Rocket占用更小的面积，使用更小的功耗，但是性能却更优。有关Rocket的详细信息将在第四章详细介绍。



表3-1 ARM Cortex-A5与采用RISC-V指令集架构的Rocket比较\[1\]

|  | ARM Cortex-A5 | RISC-V Rocket | Ratio |
| :--- | :--- | :--- | :--- |
| 寄存器宽度 | 32 | 64 | 2 |
| 主频 | &gt;1Ghz | &gt;1GHz | 1 |
| Dhrystone | 1.57DMIPS/MHz | 1.72DMIPS/Hz | 1.1 |
| 面积（不包含Cache） | 0.27mm2 | 0.14mm2 | 0.5 |
| 面积（包含16KBCache） | 0.53mm2 | 0.39mm2 | 0.7 |
| 动态功耗 | &lt;0.08 mW/MHz | 0.034 mW/MHz | &gt;0.4 |



### **3.1.2 超标量乱序执行处理器——BOOM**

BOOM（Berkeley Out-of-Order Machine）是UCB设计的一款64位超标量、乱序执行处理器，支持RV64G，也是采用Chisel编写，利用Chisel的优势，只使用了9000行代码，并且服用了Rocket的大量代码。其流水线可以划分为6级，分别是取指、译码/重命名/指令分配、发射/读寄存器、执行、访存、回写。如图3-1所示。

![](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml\wpsD817.tmp.jpg)

图3-1 BOOM处理器的流水线\[3\]



借助于Chisel，BOOM是可参数化配置的超标量处理器，可配置的参数包括：

* 取指、译码、提交、指令发射的宽度
* 重排序缓存ROB（Re-Order Buffer）、物理寄存器的大小
* 取指令缓存、RAS、BTB、加载、存储队列的深度
* 有序发射还是无序发射
* L1 cache的路数
* MSHRs（Miss Status Handling Registers）的大小
* 是否使能L2 Cache

UCB已经在40nm工艺上对BOOM进行了流片，测试结果如表3-2所示。可见BOOM与商业产品ARM Cortex-A9的性能要略优，体现在面积小、功耗低。



表3-2 BOOM与ARM Cortex-A9的性能对比\[2\]

|  | ARM Cortex-A9 | RISC-V BOOM-2W |
| :--- | :--- | :--- |
| ISA | 32bit ARM v7 | 64bit RISC-V（RV64G） |
| 微架构 | 2wide，4发射乱序处理器 | 2wide，3发射乱序处理器 |
| 流水线级数 | 8 | 6 |
| 性能 | 3.59 CoreMarks/MHz | 3.91 CoreMarks/MHz |
| 工艺 | 台积电40nm | 台积电40nm |
| 面积（含32K缓存） | 约2.5 mm2 | 约1.00 mm2 |
| 主频 | 1.4GHz | 1.5GHz |
| 功耗 | 0.5-1.9 W \(2 cores + L2\)@ TSMC 40nm, 0.8-2.0 GHz | 0.25 W \(1 core + L1\)@ TSMC 45nm, 1 GHz |

### **3.1.3 处理器家族——SHAKTI**

SHAKTI\[4\]是印度理工学院的一个计划，目标是设计一系列适合不同应用环境的、基于RISC-V的开源处理器，以及一些IP核，以便搭建SoC。这些处理器是E-Class、C-Class、I-Class、M-Class、S-Class、H-Class、T-Class、N-Class。

* E-Class：32位标量处理器，3级流水线，支持RISC-V的C（Compress）扩展，目标是超低功耗处理器。
* C-Class：32位或者64位标量处理器，3-8级流水线，支持MMU、具有容错功能、支持RISC-V的C扩展，目标也是超低功耗处理器。
* I-Class：64位、1-8核，乱序执行处理器，共享L2 Cache、支持双线程、SIMD/VPU，目标是通用处理器。
* M-Class：I-Class的增强版，增加了指令发射大小、支持四线程、最高支持16核，目标是通用处理器、低端服务器和移动应用。
* S-Class：64位、超标量多线程处理器，支持L3 Cache、RapidIO、HMC（Hybrid Memory Cube）、向量处理单元，还有协处理器用于数据库访问、加密算法、机器学习，最高支持64核，目标是通用处理器、服务器。
* H-Class：64位、32-128核、支持多线程、顺序或者乱序执行处理器，具有向量处理单元，目标是高性能计算。
* T-Class：64或者128位处理器，其中通过为存储器引入Tag，从而增强其安全性。
* N-Class：目标是通过自定义的扩展进行网络数据处理。

截至2017年7月，已经开源的是E-Class、C-Class、I-Class的测试版本，遵循的是RISC-V用户级规范2.0、特权级规范1.7，使用Bluespec System Verilog编写。以I-Class为例，其是一款64位、双发射乱序执行处理器，流水线分为8级，分别是取指、译码、重命名、唤醒（Wakeup）、选择（Select）、驱动（Drive）、执行、提交。

SHAKTI虽然计划宏伟，但是相关的文档很少，学习难度较大。



### **3.1.4 嵌入式应用处理器——ORCA**

PicoRV32是由VectorBlox公司设计的一款32位标量处理器，目标是应用于嵌入式领域，采用VHDL编写，实现了RV32IM，也可以移除其中的M扩展，也就是移除乘法除法扩展，从而减少芯片占用资源，甚至可以移除与定时器有关的指令，从而仅仅实现RV32E。当将ORCA作为一个软核下载到FPGA上的时候，其资源占用与主频如表5所示。

表5 ORCA不同配置时的资源占用与主频\[5\]

（以Altera's Cyclone IV为目标FPGA）

| ORCA配置 | 最高主频 | LUT4占用 |
| :--- | :--- | :--- |
| RV32E | 138MHz | 1700 |
| RV32I | 133.53MHz | 1900 |
| RV32IM | 97MHz | 2500 |
| RV32IM（除法不使用硬件实现） | 101MHz | 2100 |

### **3.1.5 其他开源处理器**

（1）RI5CY

RI5CY是由苏黎世联邦理工大学和波罗尼亚大学联合设计的一款小巧的4级流水线开源处理器，实现了RV32IC，以及RV32M中乘法指令mul，其目标是作为并行超低功耗处理器项目PULP（Parallel Ultra Low Power）的处理器核，所以RI5CY在RISC-V的基础上增加了许多扩展，包括硬件循环、乘累加、高级算术指令等。采用UMC的65nm工艺进行流片，RI5CY主频可以达到654MHz，动态功耗是17.5uW/MHz\[6\]。采用SystemVerilog编写。

（2）RIDECORE

RIDECORE \(RIsc-v Dynamic Execution CORE\) 是由东京工业大学设计发布的一款超标量乱序执行处理器，实现了RV32IM，6级流水线，分别是取指、译码、指令分配、发射、执行、提交，可以同时取两条指令、对两条指令译码、提交两条指令。采用的是Gshare分支预测机制。

（3）Hwacha

Hwacha是由UCB开发的一款向量处理器，UCB将Hwacha作为RISC-V的一个非标准扩展Xhwacha，已经以28nm和45nm的工艺流片多次，主频在1.5GHz以上，目前还在研发中，正在修改OpenCL的编译器，以适合Hwacha，UCB计划以开源的形式发布其代码。

（4）f32c

f32c是由萨格勒布大学设计发布的32位、5级流水线、标量处理器，原本实现的是MIPS指令集，后来添加实现了RISC-V指令集，处理器包括分支预测、直接映射缓存，同时发布的还有SDRAM控制器、SRAM控制器、视频FrameBuffer、SPI控制器、UART、GPIO等IP，使用VHDL编写代码。使用f32c处理器核，萨格勒布大学发布了FPGArduino项目，该项目将一块FPGA开发板变为一个Arduino板，并且可以使用Arduino IDE进行程序编译下载。

（5）Z-scale/V-scale

Z-scale是UCB发布的针对嵌入式环境的32位、3级流水线、单发射标量处理器，实现了RV32IM，指令总线和数据总线都是AHB-Lite。Z-scale采用是Chisel编写代码，利用Rocket中的代码，仅增加了604行代码就实现了Z-scale。V-scale是Z-scale对应的Verilog版本。

（6）sodor

sodor是UCB发布的针对教学的32位开源处理器系列，采用Chisel编码实现，可以很容易的得到对应的C++模拟器。sodor系列有五种处理器，分别是单周期处理器、2级流水线处理器、3级流水线处理器、5级流水线处理器、可执行微码的处理器。

（7）PicoRV32

PicoRV32是由RISC-V开发者Clifford Wolf设计发布的一款大小经过优化的开源处理器，实现了RV32IMC，并且根据不同环境可配置为实现RV32E、RV32I、RV32IC、RV32IM、RV32IMC。内置一个可选择的中断控制器。其特点是小巧，在Xilinx7系列芯片上占用750-2000个LUT，速度可以达到250-400MHz。PicoRV32采用Verilog编写代码。

（8）Tom Thumb

Tom Thumb是由RISC-V开发者maikmerten设计发布的一款32位、6级流水线开源处理器，实现了RV32I，目标是尽量减少FPGA的资源占用，在Cyclone IV系列FPGA上大约占用资源1200 LEs。采用VHDL编写代码。

（9）FlexPRET

FlexPRET\[7\]是由UCB设计发布的5级流水线、多线程处理器，目标是使用在实时嵌入式应用中，线程数量可配置为1-8。为了提高嵌入式处理器的资源利用率，每个硬件线程被标记为硬实时（hard real-time thread）或者软实时（soft real-time thread），硬实时线程按照固定的频率被调度，如果当前没有硬实时线程可调度，再调度软实时线程。使用Chisel编写代码。

（10）YARVI

YARVI（Yet Another RISC-V Implementation）是由RISC-V开发者Tommy Thorn设计发布的一款简单的、32位开源处理器，实现了RV32I，使用Verilog作为开发语言。其出发点不在于性能，而是要能够清晰、准确的实现RV32I。

