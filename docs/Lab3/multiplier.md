# 乘法器


## 前置知识
在这一实现中，我们需要32次“循环”来完成一次32位乘法，并得到一个64位的结果。

**开始时将“乘数”放在 Product 的低32位**：关注到每一次循环我们都需要对“乘数”进行一次右移操作，同时每一次循环我们都对 Product 进行一次右移且低32位不参与加法计算。

我们需要一个控制逻辑来判断参与加法的是**0**还是**被乘数**，同时需要判断何时停止运算。运算结束的条件很简单，即完成了32次循环，只需要一个计数器即可实现。在我们的算法中，如果乘数的***最低位***是1则给 Product 的高32位加一个被乘数；否则，不需要改变 Product 的值。

## 模块实现

你的模块端口可以参考如下方式设计：

```verilog title="multiplier.v"
module multiplier(
  input           clk,      // 时钟信号
  input           start     // 开始运算
  input [31:0]    A,        // 两个 32-bit 输入值
  input [31:0]    B,
  output          finish,   // 当结束计算时置1，你可能需要将它改为 `output reg`
  output[63:0]    res       // 64-bit 输出，你可能需要将它修改为 `output reg[63:0]`
);
```



同时，我们为你提供了一份参考样例：

~~~verilog
module multiplier(
  input clk,
  input start,
  input[31:0] A,
  input[31:0] B,
  output reg finish,
  output reg[63:0] res
);

  reg state; // 记录 multiplier 是不是正在进行运算
  reg[31:0] multiplicand; // 保存当前运算中的被乘数

  reg[4:0] cnt; // 记录当前计算已经经历了几个周期（运算与移位）
  wire[5:0] cnt_next = cnt + 6'b1;

  reg sign = 0; // 记录当前运算的结果是否是负数

  initial begin
    res <= 0;
    state <= 0;
    finish <= 0;
    cnt <= 0;
    multiplicand <= 0;
  end

  always @(posedge clk) begin
    if(~state && start) begin
      // Not Running
      sign <= ...;
      multiplicand <= ...;
      res <= ...;
      state <= ...;
      finish <= ...;
      cnt <= ...;
    end else if(state) begin
      // Running
      // Why not "else"?
      // 你需要在这里处理“一次”运算与移位
      cnt <= ...;
      res <= ...;
    end

    // 填写 cnt 相关的内容，用 cnt 查看当前运算是否结束
    if(...) begin
      // 得到结果
      cnt <= ...;
      finish <= ...;
      state <= ...;
      res <= ...;
    end
  end

endmodule
~~~



值得一提的是，这里提供的只是一个可以选择的代码框架，你可以根据自己的想法任意地进行修改，不必严格参考这份代码的实现。

关于乘法器设计是否有符号以及是否进行溢出判断部分，可以由你自行设计。只要你在验收时和实验报告中提及即可。关于这部分更详细的内容可以参考[更进一步](Lab3/more)



## 仿真测试

这里提供一份简单的参考仿真代码，你可以在此基础上进行修改或自行书写一个合适的仿真代码。

~~~verilog
module multiplier_tb;

  reg clk, start;
  reg[31:0] A;
  reg[31:0] B;

  wire finish;
  wire[63:0] res;

  multiplier m0(.clk(clk), .start(start), .A(A), .B(B), .finish(finish), .res(res));

  initial begin
    clk = 0;
    start = 0;
    #10;
    A = 32'd1;
    B = 32'd0;
    #10 start = 1;
    #10 start = 0;
    #300;

    A = 32'd10;
    B = 32'd30;
    #10 start = 1;
    #10 start = 0;
    #300;

    A = 32'd66;
    B = 32'd23;
    #10 start = 1;
    #10 start = 0;
    #500;
    $finish();
  end

  always begin
    #2 clk = ~clk;
  end

endmodule
~~~

