# 除法器

你可以从理论课学到的几个版本中任选一个算法完成实验，我们希望你的除法器可以满足**无符号、无溢出判断、有除零异常判断**的要求，但也允许你做适当的调整。

## 模块实现

这里给出除法器模块的一个参考接口：

```verilog title="divider.v"
module divider(
    input   clk,
    input   rst,
    input   start,          // 开始运算
    input[31:0] dividend,   // 被除数
    input[31:0] divisor,    // 除数
    output divide_zero,     // 除零异常
    output  finish,         // 运算结束信号
    output[31:0] res,       // 商
    output[31:0] rem        // 余数
);
```



如果除数是零，则直接将 `finish` 与 `divide_zero` 升至高位（赋值为1）即可，此时 `res` 与 `rem` 可以是任意值。

!> 你可以尝试把一些信号输出接在LED灯上，方便观察与验收



## 仿真测试

下面给出一份基本的测试代码，你可以添加更多的测试样例，并尽可能多测试边界情况。

~~~verilog
`timescale 1ns / 1ps

module divider_tb();
      reg clk;
      reg rst;
      reg [31:0] dividend;
      reg [31:0] divisor;
      reg start;
      
      wire divide_zero;
      wire [31:0] res;
      wire [31:0] rem;
      wire finish;
      divider   u_div(
         .clk(clk),
         .rst(rst),
         .dividend(dividend),
         .divisor(divisor),
         .start(start),
         .divide_zero(divide_zero),
         .res(res),
         .rem(rem),
         .finish(finish)
      );
      always #5 clk = ~clk;
      
      initial begin
       clk =0;
       rst = 1;
       start = 0;
       #10
       rst = 0;
       start = 1;
           dividend = 32'd8;
           divisor  = 32'd4;
           #335
           dividend = 32'd100;  
           divisor  = 32'd10;   
           #335     
           dividend = 32'd9;
           divisor  = 32'd4; 
           #340            
           dividend = 32'd100; 
           divisor  = 32'd99;  
           #350 $stop();   
      
      end
endmodule
~~~

