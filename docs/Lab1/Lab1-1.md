# Lab1-1

在本实验中，我们会进行ALU和Regfiles的设计。其中，ALU作为计算的核心部件，会在之后的CPU core中帮助CPU处理加法、减法、位移等运算。而Regfiles则是我们CPU上的寄存器，用于高效存储程序运行过程的数值。



## ALU

ALU (Arithmetic Logic Unit) 是负责对二进制整数进行算术运算和位运算的组合电路部件，本实验要求你设计一个32位的能够对两个输入进行：按位与/或/异或、无符号数加法/减法/溢出、逻辑右移。



### 模块实现

> 报告中需要给出你写出这部分的完整代码。

你可以在两种实现方式中选择一种方式来实现：

* 采用结构化描述，利用IP核或各组件的源代码编写ALU模块
* 根据[更进一步](#更进一步)中的说明自行设计ALU相应的模块与内容，为之后的实验做好准备

为了方便之后的实验，这里给出一个模块名与端口名命名接口的参考范式，你也可以不采用这种方式进行命名，只要程序能够正确运行，并且在实验报告中说明清楚每个变量的含义即可。

```verilog
module ALU(
    input wire [31:0] A,
    input wire [31:0] B,
    input wire [3:0] control,

    output reg [31:0] C,
    output wire zero,
    output wire sign,
    output wire carry
);
// Your code

endmodule
```



### 仿真测试

> 报告中需要给出你所使用的仿真测试代码，测试波形与解释（波形截图需要保证缩放与变量数制合适）。

基础的仿真波形已经在附件 `ALU/ALU_tb.v`，但它过于简单，你需要添加若干边界测试以完善。

?> 请思考:   如果要对 ALU 进行拓展，要求修改 ALU 以**支持两个有符号数的大小比较**，你需要添加哪些端口以支持？（Hint：目前的ALU将两个输入都视作无符号数； `zero` 信号仅能用来判断是否相等）



### 更进一步

在RV32I指令集中，每一条指令都是32 bit。其中，如果一条指令是需要使用ALU来进行运算的话，我们会需要4位来确定这条指令在ALU上究竟进行哪一个运算。在[RISC-V的手册](http://riscvbook.com/chinese/RISC-V-Reader-Chinese-v2p1.pdf)上对这部分的内容进行了详细的说明。如果你期望可以将ALU一步设计到位的话，可以参考手册中第25页上对RISC-V指令集的介绍来设计ALU中的op code。



## Register Files

RV32I 可以使用的整型寄存器共有32个，本节将实现一个有32个 32-bit 寄存器的寄存器组。



### 模块实现

> 报告中需要给出你写出这部分的完整代码。

Register File 需要实现读写指定寄存器的功能，并保证一定的时序。

考虑一个简单的指令 `add x1, x2, x3`，它的含义是计算寄存器 `x2` 与 `x3` 的和，并将结果保存到寄存器 `x1` 中；在这个指令中 `x2, x3` 被称为源寄存器(Source Registers, `rs`)，`x1` 被称为目的寄存器(Destination Register, `rd`)。

观察到，我们有可能在一个指令运算中需要读取两个源寄存器的值，因此需要同时支持两个读口；本次实验只需要在时钟**上升沿**进行一次写，即一个时钟周期只进行最多一次的寄存器写。

如果你对 Verilog 的结构和语法很生疏，请先学习基础内容，如 `always` 块、赋值等。你可以在前面的[章节](/preface/prerequisites)中找到相应的参考资料。

这里给出一个模块名与端口名命名接口的参考范式，你也可以不采用这种方式进行命名，只要程序能够正确运行，并且在实验报告中说明清楚每个变量的含义即可。

```verilog linenums="1" title="Regs.v"
module regs(
    input wire clk, rst, RegWrite,
    input wire [4:0] Rs1_addr, Rs2_addr, Write_addr,
    input wire [31:0] Write_data,

    output wire [31:0] Rs1_data, Rs2_data
);
// Your code here

endmodule
```



### 仿真测试

> 报告中需要给出你所使用的仿真测试代码，测试波形与解释（波形截图需要保证缩放与变量数制合适）。

附件 `Regs/Regs_tb.v` 给出了基本的测试代码，你需要补充完善测试更多情况。



### 挑战一下

在我们所设计的Regfile中，为了方便大家之后对寄存器堆的内容进行调试，你可以尝试为你所设计的寄存器堆增加31个引脚输出每个寄存器中所存储的值。

本部分内容不作为实验的强制要求，但增加该部分的内容对你今后的调试将有很大的帮助，因此我们非常建议你完成该部分的内容。

加入了这部分内容之后，你的Regfiles模块的代码大概会长成这样：

~~~verilog
module regs(
    input wire clk, rst, RegWrite,
    input wire [4:0] Rs1_addr, Rs2_addr, Write_addr,
    input wire [31:0] Write_data,

    output wire [31:0] Reg00,
    output wire [31:0] Reg01,
    output wire [31:0] Reg02,
    ...
    output wire [31:0] Reg31,

    output wire [31:0] Rs1_data, Rs2_data
);
~~~

