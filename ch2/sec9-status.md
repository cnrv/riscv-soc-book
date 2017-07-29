## **3 基于risc-v架构的开源soc**

### **3.3.1 Rocket-Chip**

UCB为了方便用户学习，同时也为了便于重复使用已设计好的硬件模块，在GitHub上建立了Rocket-Chip Generator的项目，其中包括了Chisel、GCC、Rocket处理器，以及围绕Rocket的一系列总线单元、外设、缓存等，并且采用了参数化的配置方法，从而可以方便的创建不同性能要求的基于Rocket处理器的SoC。采用Chisel编写，主要的子模块如下。

* Chisel：UCB设计的开源硬件编程语言。
* Hardfloat：参数可配置的、兼容IEEE 754-2008标准的浮点单元。
* Riscv-tools：开发工具，包括GCC、Newlib，以及移植的Linux。
* Rocket：Rocket处理器，包括L1 Cache。
* Uncore：实现了需要与Rocket紧密连接的功能单元，比如L2 Cache、L1 Coherence Hub等。
* Juntions：实现了不同协议的接口之间的转换。
* Rocketchip：顶层模块，同时也实现了内部总线TileLink向外部总线AXI或者AHB的转换。

前文介绍的BOOM、Z-scale都可以通过配置Rocket-Chip的不同参数得到。

### **3.3.2 LowRISC**

LowRISC是由剑桥大学为主的一些研发人员成立的非营利性组织，主要是设计发布基于RISC-V指令集的64位开源SoC，其成员有树莓派的合作者，所以其目标是希望将设计的SoC做成类似于树莓派那样价格便宜、功能丰富、拥有大量用户的开源硬件。LowRISC发布的SoC的名称也是LowRISC，是在Rocket-Chip的基础上改进开发的，采用System Verilog编写改进部分的代码。主要特点是：

（1）Tagged Memory：给每一个存储位置都增加了一个Tag，目前是双字（64bit）对应一个Tag（4bit），目的是防止控制流劫持攻击，同时也有其他的一些用处，比如：垃圾回收、设置watchpoint等。为了实现Tagged Memory，LowRISC为RISC-V增加了两条指令用来读写Tag。2015年4月发布的0.1版本中具有该功能。

（2）Untethered：早期的Rocket-Chip需要依赖于一个通用处理器的协助才能够启动，才能够访问串口、网口、SD卡等外设，Untethered LowRISC通过实现（Memory mapping I/O）、片上NASTI interconnect等功能，解决了上述问题。2015年11月发布的0.2版本中具有该功能。

（3）Trace Debugging：引入了Open SoC Debug，支持Trace Debugging，可以收集指令执行记录，便于离线或者在线分析。2016年7月发布的0.3版本中具有该功能。

### **3.3.3 PULPino**

PULPino是苏黎世联邦理工大学和波罗尼亚大学联合发布的基于RISC-V的开源处理器，其处理器核RI5CY在前文已述，苏黎世联邦理工大学和波罗尼亚大学本来设计的项目是PULP，这是一个多核SoC项目，考虑到这个项目太复杂，有许多IP、自定义工具集，不方便开源，所以开发者决定先开源一个单核SoC项目，即PULPino。PULPino直接使用了PULP项目的许多IP。

PULPino具有一个AXI互连总线，另外还有一个APB总线，用来连接低速外设，比如：GPIO、UART、I2C控制器、SPI Master控制器等。调试模块支持Advanced Debug Unit。PULPino包括一个Boot ROM，其中可以写入BootLoader，从而实现在启动的时候从外部Flash读入程序并执行。

### **3.3.4 RISC-V VHDL**

RISC-V VHDL是俄罗斯的GNSS Sensor公司发布的基于Rocket的开源SoC，其前身是莫斯科物理技术学院的一个项目。该项目的处理器核直接就用的是Rocket，可以配置为只有L1Cache，也可以配置为包括L2Cache，在此基础上，提供了大量的IP核，采用类似LEON3的GRLIB库的方式，所有的IP核都是即插即用，RISC-V VHDL提供了一个AXI总线，IP核都挂载在该总线上。IP核包括：UART、GPIO、中断控制器、以太网控制器，此外还支持DSU（Debug Support Unit），均采用VHDL编写代码。

RISC-V VHDL中大多数IP核都是开源的，唯一商业的是GNSSLIB，这是一个与定位导航有关的库，也是RISC-V VHDL的特色。

