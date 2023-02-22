# 前置知识

本节介绍了进行《计算机组成》实验可能所需的部分前置知识。如果你对此并不了解, 建议你先花一些时间进行学习。

## Verilog语法简介

根据以往经验，在实验过程中，大家经常会因为Verilog语法而在实现过程中出现问题。比如最常见的问题就是阻塞赋值和非阻塞赋值的错误使用，导致仿真和上板跑的结果不一样。

如果不熟悉Verilog的，推荐大家去做一下[hdlbits](https://hdlbits.01xz.net/wiki/Main_Page)里面的verilog练习，应该不会花太长时间，但是对你熟悉Verilog很有帮助。

这里仅介绍部分本学期中大家可能会使用到的一些Verilog语法。

* 变量
  * verilog里面有两种类型的变量，一种是wire一种是reg。
    * wire物理上就是电路中的一条连线，不能hold值
    * reg是可以保存值的

* 向量

  * vector既可以是wire构成的，也可以是reg构成的。vector有一些比较常用的操作，如拼接、重复、规约等。

  * **拼接concatenation**, 可以用大括号将vector拼接起来。

    * ~~~verilog
      input [15:0] in;
      output [23:0] out;
      //left
      assign {out[7:0], out[15:8]} = in;
      //right
      assign out[15:0] = {in[7:0], in[15:8]};
      assign out = {in[7:0], in[15:8]};
      
      ~~~

  * **重复replication**，可以将一个vector复制n份，比如你想将一个1扩展到5位。

    * ~~~verilog
      {5{1'b1}} // 5'b11111 (or 5'd31 or 5'h1f)
      {2{a,b,c}} // The same as {a,b,c,a,b,c}
      {3'd5, {2{3'd6}}} // 9'b101_110_110
      ~~~

  * **规约reduction**，如果你想对一个vector里面的每一位与起来，或起来，异或起来的话，不需要每一位每一位写过去，直接用这个reduction operator

    * ~~~verilog
      & a[3:0]     // AND: a[3]&a[2]&a[1]&a[0]. Equivalent to (a[3:0] == 4'hf)
      | b[3:0]     // OR:  b[3]|b[2]|b[1]|b[0]. Equivalent to (b[3:0] != 4'h0)
      ^ c[2:0]     // XOR: c[2]^c[1]^c[0]
      ~~~

* 赋值

  * Verilog常用有三种类型的赋值，连续赋值，阻塞赋值和非阻塞赋值
  * Continuous assignment (assign x = y;)
    * 连续赋值就是assign x = y；这个形式。连续赋值不能出现在过程块中，比如always block。同时，assign语句的左侧必须为wire，不能是reg。

  * Procedural blocking assignment (x = y;)
    * 阻塞赋值是=号的形式。阻塞赋值只能被用在过程语句中，比如always block, initial。在always语句中，最好只在`alway @(*)`中使用。在使用阻塞赋值的时候，左侧必须为reg类型

  * Procedural non-blocking assignment (x <= y;)
    * 非阻塞赋值是一个<=号的形式。和阻塞赋值一样，非阻塞赋值只能被用在always initial这种过程语句中，左侧必须为reg类型。但和阻塞赋值不同的是，在always语句中，最好只在`alway @(clk)`中使用

* always block

  * 我们主要用的always block有两种类型，一种是组合的，always@(*)；一种是时钟的, always @(posedge clk)
  * always @(\*)是一种组合逻辑，和assign语句是等价的。但是always @*中可以使用更丰富的语句，比如可以使用if-else, case语句。因此，如果想要描述一些复杂的组合逻辑，推荐使用always @(\*)
  * always @(posedge clk)语句主要是用来描述时序逻辑的，绝大多数情况适用非阻塞赋值




