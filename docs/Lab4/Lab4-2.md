# Lab4-2

## 实验内容

当本实验完成时，你的SCPU模块就已经是完全由我们自己实现的了。为了便于你在实验过程中的调试和实验的验收，如果你在之前的实验中并未完成如下内容的话，你可以尝试本次实验中补充完成下列内容，以方便你进行调试：

- 在DataPath种使用含32读口的RegFile，读到的Debug数据需要一路引出到SCPU的输出。
- SCPU得到的Debug信号成为添加了Debug输入口的VGA模块的输入，在Top中进行连接。

## 指令要求

本次实验需要实现的指令如下，你可以自行扩展实现更多的指令，但我们不会在本次实验对其他指令验收。

- R-Type: add, sub, and, or, xor, slt, srl
- I-Type: addi, andi, ori, xori, slti, srli, lw
- S-Type: sw
- B-Type: beq
- J-Type: jal

其他指令将在 Lab4-3 指令拓展与 Lab4-4 中断处理两小节实现。如果不清楚以上指令的指令格式与含义，请查看 RISC-V 手册。



## 仿真代码

为了方便你进行测试，我们需要搭建一个简单的仅包含 SCPU 以及 Mem(IMem & DMem) 的测试平台。

这里提供了一份参考的仿真测试模块与仿真代码，如果你的端口名与之不同请自行修改。当然，你可以选择不使用该代码进行仿真。

使用该模块进行仿真时，请自行书写测试代码，生成 ROM 核，并进行仿真。


!> 经测试，该仿真模块无法直接对所提供的IP核进行仿真，请在完成Lab4-1和Lab4-2的全部内容后再使用该模块进行仿真验证。



测试模块：`testbench.v`

```verilog
module testbench(
    input clk,
    input rst
);

    /* SCPU 中接出 */
    wire [31:0] Addr_out;
    wire [31:0] Data_out;       
    wire        CPU_MIO;
    wire        MemRW;
    wire [31:0] PC_out;
    /* RAM 接出 */
    wire [31:0] douta;
    /* ROM 接出 */
    wire [31:0] spo;

    SCPU u0(
        .clk(clk),
        .rst(rst),
        .Data_in(douta),
        .MIO_ready(CPU_MIO),
        .inst_in(spo),
        .Addr_out(Addr_out),
        .Data_out(Data_out),
        .CPU_MIO(CPU_MIO),
        .MemRW(MemRW),
        .PC_out(PC_out)
    );

    RAM_B u1(
        .clka(~clk),
        .wea(MemRW),
        .addra(Addr_out[11:2]);
        .dina(Data_out),
        .douta(douta)
    );

    ROM_B u2(
        .a(PC_out[11:2]),
        .spo(spo)
    );

endmodule
```



仿真文件：`testbench_tb.v`

```verilog
module testbench_tb();

    reg clk;
    reg rst;

    testbench(.clk(clk), .rst(rst));

    initial begin
        clk = 1'b0;
        rst = 1'b1;
        #5;
        rst = 1'b0;
    end

    always #50 clk = ~clk;

endmodule
```





## 验收代码

- 汇编代码

  - 该代码不是验收的强制要求，你也可以采用自己编写的代码来完成验收，但希望你的测试能够覆盖我们所要求的所有指令，并在验收时和实验报告中提供相应的解释。

  - RISC-V汇编代码，如果你希望用Visual Studio Code查看，你可以尝试安装`RISC-V Extension`插件来实现代码高亮。


  ```
  jal zero, start # 0
  add zero, zero, zero # 4
  add zero, zero, zero # 8
  add zero, zero, zero # C
  add zero, zero, zero # 10
  add zero, zero, zero # 14
  add zero, zero, zero # 18
  add zero, zero, zero # 1C
  
  start:
  addi x1, zero, -1 # x1=FFFFFFFF
  xori x3, x1, 1 # x3=FFFFFFFE
  add x3, x3, x3 # x3=FFFFFFFC
  add x3, x3, x3 # x3=FFFFFFF8
  add x3, x3, x3 # x3=FFFFFFF0
  add x3, x3, x3 # x3=FFFFFFE0
  add x3, x3, x3 # x3=FFFFFFC0
  xor x20, x3, x1 # x20=0000003F
  add x3, x3, x3 # x3=FFFFFF80
  add x3, x3, x3 # x3=FFFFFF00
  add x3, x3, x3 # x3=FFFFFE00
  add x3, x3, x3 # x3=FFFFFC00
  add x3, x3, x3 # x3=FFFFF800
  add x3, x3, x3 # x3=FFFFF000
  add x3, x3, x3 # x3=FFFFE000
  add x3, x3, x3 # x3=FFFFC000
  add x3, x3, x3 # x3=FFFF8000
  add x3, x3, x3 # x3=FFFF0000
  add x3, x3, x3 # x3=FFFE0000
  add x3, x3, x3 # x3=FFFC0000
  add x3, x3, x3 # x3=FFF80000
  add x3, x3, x3 # x3=FFF00000
  add x3, x3, x3 # x3=FFE00000
  add x3, x3, x3 # x3=FFC00000
  add x3, x3, x3 # x3=FF800000
  add x3, x3, x3 # x3=FF000000
  add x3, x3, x3 # x3=FE000000
  add x3, x3, x3 # x3=FC000000
  add x6, x3, x3 # x6=F8000000
  add x3, x6, x6 # x3=F0000000
  add x4, x3, x3 # x4=E0000000
  add x13, x4, x4 # x13=C0000000
  add x8, x13, x13 # x8=80000000
  ori x26, zero, 1 # x26=00000001
  andi x26, x26, 0xff
  srl x27, x8, x26
  
  loop:
  slt x2, x1, zero # x2=00000001 针对ALU32位有符号数减
  slti x2, x1, 0 # x2=00000001 针对ALU32位有符号数减
  add x14, x2, x2
  add x14, x14, x14 # x14=4
  sub x19, x14, x14 # x19=0
  srli x19, x19, 1
  addi x10, x19, -1
  or x10, x10, zero
  add x10, x10, x10 # x10=FFFFFFFE
  
  loop1:
  sw x6, 0x4(x3) # 计数器端口: F0000004, 送计数常数x6=F8000000
  lw x5, 0x0(x3) # 读GPIO端口F0000000状态: {counter0_out,counter1_out,counter2_out,led_out[12:0], SW}
  add x5, x5, x5 # 左移
  add x5, x5, x5 # 左移2位将SW与LED对齐, 同时D1D0置00, 选择计数器通道0
  sw x5, 0x0(x3) # x5输出到GPIO端口F0000000, 设置计数器通道counter_set=00端口
  add x9, x9, x2 # x9=x9+1
  sw x9, 0x0(x4) # x9送x4=E0000000七段码端口
  lw x13, 0x14(zero) # 取存储器20单元预存数据至x13, 程序计数延时常数
  
  loop2:
  lw x5, 0x0(x3) # 读GPIO端口F0000000状态: {out0, out1, out2, D28-D20, LED7
  add x5, x5, x5
  add x5, x5, x5 # 左移2位将SW与LED对齐, 同时D1D0置00, 选择计数器通道0
  sw x5, 0x0(x3) # x5输出到GPIO端口F0000000, 计数器通道counter_set=00端口不变
  lw x5, 0x0(x3) # 再读GPIO端口F0000000状态
  and x11, x5, x8 # 取最高位=out0, 屏蔽其余位送x11
  add x13, x13, x2 # 程序计数延时
  beq x13, zero, C_init # 程序计数x13=0, 转计数器初始化, 修改7段码显示: C_init
  
  l_next: # 判断7段码显示模式：SW[4: 3]控制
  lw x5, 0x0(x3) # 再读GPIO端口F0000000开关SW状态
  add x18, x14, x14 # x14=4, x18=00000008
  add x22, x18, x18 # x22=00000010
  add x18, x18, x22 # x18=00000018(00011000)
  and x11, x5, x18 # 取SW[4: 3]
  beq x11, zero, L20 # SW[4: 3]=00, 7段显示"点"循环移位：L20, SW0=0
  beq x11, x18, L21 # SW[4: 3]=11, 显示七段图形, L21, SW0=0
  add x18, x14, x14 # x18=8
  beq x11, x18, L22 # SW[4: 3]=01, 七段显示预置数字, L22, SW0=1
  sw x9, 0x0(x4) # SW[4: 3]=10, 显示x9, SW0=1
  jal zero, loop2
  
  L20:
  beq x10, x1, L4 # x10=ffffffff, 转移L4
  jal zero, L3
  
  L4:
  addi x10, zero, -1 # x10=ffffffff
  add x10, x10, x10 # x10=fffffffe
  
  L3:
  sw x10, 0x0(x4) # SW[4: 3]=00, 7段显示点移位后显示
  jal zero, loop2
  
  L21:
  lw x9, 0x60(x17) # SW[4: 3]=11, 从内存取预存七段图形
  sw x9, 0x0(x4) # SW[4: 3]=11, 显示七段图形
  jal zero, loop2
  
  L22:
  lw x9, 0x20(x17) # SW[4: 3]=01, 从内存取预存数字
  sw x9, 0x0(x4) # SW[4: 3]=01, 七段显示预置数字
  jal zero, loop2
  
  C_init:
  lw x13, 0x14(zero) # 取程序计数延时初始化常数
  add x10, x10, x10 # x10=fffffffc, 7段图形点左移121 or x10, x10, x2 # x10末位置1, 对应右上角不显示
  add x17, x17, x14 # x17=00000004, LED图形访存地址+4
  and x17, x17, x20 # x17=000000XX, 屏蔽地址高位, 只取6位
  add x9, x9, x2 # x9+1
  beq x9, x1, L6 # 若x9=ffffffff, 重置x9=5
  jal zero, L7
  
  L6:
  add x9, zero, x14 # x9=4
  add x9, x9, x2 # 重置x9=5
  
  L7:
  lw x5, 0x0(x3) # 读GPIO端口F0000000状态
  add x11, x5, x5
  add x11, x11, x11 # 左移2位将SW与LED对齐, 同时D1D0置00, 选择计数器通道0
  sw x11, 0x0(x3) # x5输出到GPIO端口F0000000, 计数器通道counter_set=00端口不变
  sw x6, 0x4(x3) # 计数器端口: F0000004, 送计数常数x6=F8000000
  jal zero, l_next
  ```

- Instruction COE

  ```
  memory_initialization_radix=16;
  memory_initialization_vector=
  0200006F, 00000033, 00000033, 00000033, 00000033,
  00000033, 00000033, 00000033, FFF00093, 0010C193,
  003181B3, 003181B3, 003181B3, 003181B3, 003181B3,
  0011CA33, 003181B3, 003181B3, 003181B3, 003181B3,
  003181B3, 003181B3, 003181B3, 003181B3, 003181B3,
  003181B3, 003181B3, 003181B3, 003181B3, 003181B3,
  003181B3, 003181B3, 003181B3, 003181B3, 003181B3,
  003181B3, 00318333, 006301B3, 00318233, 004206B3,
  00D68433, 00106D13, 0FFD7D13, 01A45DB3, 0000A133,
  0000A113, 00210733, 00E70733, 40E709B3, 0019D993,
  FFF98513, 00056533, 00A50533, 0061A223, 0001A283,
  005282B3, 005282B3, 0051A023, 002484B3, 00922023,
  01402683, 0001A283, 005282B3, 005282B3, 0051A023,
  0001A283, 0082F5B3, 002686B3, 06068063, 0001A283,
  00E70933, 01290B33, 01690933, 0122F5B3, 00058C63,
  03258663, 00E70933, 03258863, 00922023, FB9FF06F,
  00150463, 00C0006F, FFF00513, 00A50533, 00A22023,
  FA1FF06F, 0608A483, 00922023, F95FF06F, 0208A483,
  00922023, F89FF06F, 01402683, 00A50533, 00E888B3,
  0148F8B3, 002484B3, 00148463, 00C0006F, 00E004B3,
  002484B3, 0001A283, 005285B3, 00B585B3, 00B1A023,
  0061A223, F6DFF06F;
  ```

- Data COE

  ```
  memory_initialization_radix=16;
  memory_initialization_vector=
  f0000000, 000002AB, 80000000, 0000003F, 00000001, FFF70000, 0000FFFF, 80000000, 00000000, 11111111, 
  22222222, 33333333, 44444444, 55555555, 66666666, 77777777, 88888888, 99999999, aaaaaaaa, bbbbbbbb, 
  cccccccc, dddddddd, eeeeeeee, ffffffff, 557EF7E0, D7BDFBD9, D7DBFDB9, DFCFFCFB, DFCFBFFF, F7F3DFFF,
  FFFFDF3D, FFFF9DB9, FFFFBCFB, DFCFFCFB, DFCFBFFF, D7DB9FFF, D7DBFDB9, D7BDFBD9, FFFF07E0, 007E0FFF,
  03bdf020, 03def820, 08002300;
  ```

1. Data COE 包含了七段数码管的显示信息，请你修改其中的16个显示数字，即修改 `DMem.coe` 中的 `00000000, 11111111, 22222222..., ffffffff` 部分，将其改为你的学号（如3210102023）和日期（如230308）。修改后，这一段的代码类似如下格式：

   ```
   33333333, 22222222, 11111111, 00000000, 11111111, 00000000, 22222222, 
   00000000, 22222222, 33333333, 22222222, 33333333, 00000000, 33333333,
   00000000, 88888888
   ```

   

2. 如果你无法得到期望结果，你或许需要理解汇编代码在做什么，TA已经给出了部分的注释，阅读起来难度应该不大，这样可以缩小你的指令错误可能范围。



## 预期表现

### Graphics Mode && `SW[4:3] = 00`

- After reset, the 7-segment LEDs are lit in turn.
- ***Only run once after reset, not loop***.
- 重启后，七段数码管依次亮起。
- 只在重启后亮一次，之后不变。



### Number Mode && `SW[4:3] = 01`

- Show your student ID and date digit by digit.
- 显示你的学号与日期



### Number Mode && `SW[4:3] = 10`

- Self-incrementing from the lowest digit.
- 数字自增



### Graphics Mode && `SW[4:3] = 11`

- Rectangle animation.
- 变化的矩形



在某模式下你的表现与预期不符，请查看该模式对应代码，缩小检查范围。
