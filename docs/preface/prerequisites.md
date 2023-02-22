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



## 如何使用 Git

Git 是一个版本控制系统 (version control system, VCS). 什么是版本控制? 为什么需要做版本控制?

想象你正在进行实验, 你决定给你的数据通路增加一条指令支持, 但添加这个支持需要修改大量之前的代码. 你一咬牙一狠心, 熬了个大夜, 终于把这个功能加完了, 编译时遇到的问题也都修好了. 第二天你信心满满地生成字节流下板验证:

*“怎么会, VGA显示板上怎么会这样.”*

你坐在宿舍的椅子上, 窗户灌进阵阵冷风, 看着 泛着蓝光的VGA界面, 你的心早就凉了.

“要是能回到加这个数据通路之前就好了.” 你想着. 但你没有粉色大猫猫, 也没有 SERN 的 LHC (和助手), 更没有时之盾牌, 那些过去的时间再也回不去了.

不过, 不幸中的万幸, 你用 Git 管理了你的代码.

你熟练地在命令行里敲下 `git reset xxx`, 瞬间, 一切回到了那天之前.

一些你需要知道的基本内容:

* **初始化 Git 仓库:** 在仓库目录中 `git init`.
* **忽略部分文件的更改:** 在对应目录中放置 `.gitignore` 文件, 并在该文件中添加需要忽略的文件的规则.
* **查看仓库状态:** `git status`.
* **暂存更改:** `git add 文件名`, 或 `git add -A` 暂存全部更改.
* **提交更改:** `git commit`, 此时会弹出默认编辑器并要求你输入提交信息. 也可以直接执行 `git commit -m "提交信息"`.
* **添加远程仓库:** `git remote add 名称 仓库URL`.
* **推送本地提交到远程:** `git push`.
* **查看所有提交记录:** `git log`, 你可以从中看到某个提交的哈希值.
* **把仓库复位到某个提交的状态:** `git reset 提交的哈希值`.
* **从当前提交新建分支并切换:** `git checkout -b 分支名`.
* **切换到分支:** `git checkout 分支名`.
* **删除分支:** `git branch -D 分支名`.

上面提到了 `.gitignore` 可以让 Git 忽略目录中某些文件, 且不让它们出现在 Git 仓库中. 这有什么用呢?

你在开发过程中难免会产生一些 **“只对你自己有用”** 且 **“不值得永久保留”** 的东西. 比如你在开发的过程中希望写几个简单的输入来测试你的程序, 或者验证你程序里的某处是否写对了, 于是你新建了个名字叫 `test.txt` 的文件, 里面写了一些测试的内容, 然后你在本地调试的时候会让你的程序读取这个文件.

`test.txt` 显然只是个用来存放写一些只对你自己有用的临时内容的文件, 你不希望让 Git 每次都记录这个文件的更改 (因为没意义), 所以你可以把它写进 `.gitignore` 中, 来让 Git 忽略它.

其他类似的情况还包括, 你使用 VS Code 或 IDEA 开发时, 这些代码编辑器/IDE 可能会在项目中生成一些配置文件 (`.vscode` 或 `.idea`), 这些文件通常也是不需要被 Git 记录的, 因为其中包含了你的一些个人配置.

推荐:

* [Learn Git Branching](https://learngitbranching.js.org).
* [Git 快速入门](https://nju-projectn.github.io/ics-pa-gitbook/ics2021/git.html).
