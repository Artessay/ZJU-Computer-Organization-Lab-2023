# Lab1-2

这部分内容与《数字逻辑设计》部分的有限状态机里的内容基本重合，重点掌握有限状态机的设计思想与方法。在我们之后许多课程的设计中都还会用到有限状态机的知识，希望大家可以好好复习一下。



## Finite State Machine

Finite State Machine（FSM，有限状态机）是在有限个状态之间按照一定规律转换状态（并给出输出）的时序电路。 

本节实验设计一个 Moore 型 FSM（Output 为现态的函数）来检测特定序列 ***1110010***，更详细的说明请查看 slides p14。

对于简单的 FSM，设计思路一般是先画出状态转移图，随后根据状态图设计相应的代码。



## 模块实现

**三段式描述**：将*输出信号*与*状态跳转*分开描述，状态跳转用组合逻辑来控制。

根据你绘制的状态图，使用三段式描述完成序列检测的任务。

你的代码主体将主要分为以下几个部分：

* 第一段主要用来控制时序，保证下一个时钟上升沿完成状态转移，同时需要注意重置时将状态变为起始状态；
* 第二段使用组合逻辑，主要是根据现态和输入决定次态，为此你可能需要使用 `case` 关键词；
* 第三段决定输出，根据你的状态转移图，将所有输出为1的状态取或即可，类似于 `assign out = (Q1 == curr_state) || (Q2 == curr_state);`。

```verilog linenums="1" title="seq.v"
module seq(
	input clk,
	input reset,
	input in,
	output out
);
// State definition
  localparam 
  	Q1 = ...,
  	Q2 = ...,
  	...;

// First segment: state transfer
  always @(posedge clk or posedge rst) begin
		...
  end

// Sencond segment: transfer condition
  always @(*) begin // combination logic
    case(curr_state)
      Q1: begin
        if(1'b0 == input) next_state = ...;
        else next_state = ...;
      end
      ...
      default: next_state = ...;
    endcase
  end

// Third segment: output
  ...

endmodule
```



## 仿真测试

请在报告中给出你的仿真测试代码，测试波形与解释（波形截图需要保证缩放与变量数制合适）。

附件 `seq_moore/seq_moore_tb.v` 已经给出了基本的测试代码，你可以直接使用，也可以适当对其修改后使用。
