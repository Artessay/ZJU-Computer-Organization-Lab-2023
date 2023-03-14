# Lab1

1. Shift指令的移位位数由寄存器B的值决定，Slides中什么关于只移动1位不用理会。由B的哪几bit决定也不关键，只要自己记住自己是用的哪几bit就可以了。

2. Regs模块增加32个固定地址读口，减少Lab4的任务量。

   类似于下面的 `module` 声明，或者可以按照Risc-V的寄存器名命名为 `x0` `ra` `sp` 等

   ```verilog
   module Regs(input clk,
               input rst,
               input [4:0] Rs1_addr,
               input [4:0] Rs2_addr,
               input [4:0] Wt_addr,
               input [31:0]Wt_data,
               input RegWrite,
               output [31:0] Reg00,
               ......
               output [31:0] Reg30,
               output [31:0] Reg31,
               output [31:0] Rs1_data,
               output [31:0] Rs2_data);
   ```

3. 封装IP时均采用含源代码方式封装。便于后续的仿真以及对引用IP的工程可以的修改。
   
4. 参考的ALU控制信号设计如下：

   ```verilog
      case (control)
         4'b0000: C<=ansAnd;
			4'b0001: C<=ansOr;
			4'b0010: C<=ansAddSub;
			4'b0110: C<=ansAddSub;
			4'b0111: C<=carry ? 32'b1 : 32'b0; //slt
			4'b1001: C<= sign ? 32'b1 : 32'b0; //sltu
			4'b1100: C<=ansXor;
			4'b1101: C<=ansSrl;
			4'b1110: C<=ansSll;
			4'b1111: C<=ansSra;
			default: C<= 32'b0;
		endcase
   ```