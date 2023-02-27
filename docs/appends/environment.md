# 实验环境使用说明

## Vivado安装

请确保有足够的磁盘容量。

💡请选择不低于 **2020.2** 的版本

在校网的环境下，你可以选择下载课程FTP网站上的Vivado 2020.2版本的文件，下载解压后可以直接安装使用。课程FTP的地址如下：ftp://10.78.18.201。由于文件相对较大，推荐使用FileZilla等FTP文件传输工具协助进行传输。FTP传输过程速度相对较慢，可能需要较长时间。



此外，这里给出另一种在官网下载Vivado的方式：

* 在官网提供的[下载网页](https://www.xilinx.com/support/download/index.html/content/xilinx/en/downloadNav/vivado-design-tools/archive.html)中找到合适的版本(版本号；Windoww/Linux)，选择“...Web Installer”（约300MB的文件）。

* 打开下载安装程序，使用注册的Xilinx账号登陆，并选择“Download and install now”
* 选择安装的软件 (Vivado) 、版本 (Vivado HL WebPACK)
* 选择需要的组件（以下为必须勾选）：Design Tools (Vivado Deign Suite, DocNav); Devices (Kintex-7); Installation Options (Install Cable Drivers)。选择完成后查看磁盘空间，使用上述选择需要30-40GB空间用来安装
  * 如果你之后需要使用其他型号的设备，可以通过 installer 补充下载，不必要一次全部安装(可以查看[ Xilinx-Support-60112 ](https://support.xilinx.com/s/article/60112))
* 确保空间充足，开始下载，并等待安装结束

> 习惯使用 Linux 系统的同学可以安装 Linux 版本，WSL2 也可尝试安装。
>
> Vivado 安装路径上**不要出现中文和空格**。



## 使用提示与实用技巧

1. 所有实验、软件均在纯英文路径下完成，且路径中不包含`空格`

2. 即使是Slide中要求使用原理图（即bd文件）完成的实验，也请改为使用Verilog代码实现所有模块
   - 可以完全避免`bd`图不稳定的bug，以及引用IP时的部分错误。就上一学年经验，这一部分bug很常见且很难排查，大多数情况都是重做解决，极其浪费时间。

3. 对于Windows系统的同学。工程和IP文件命名尽量简短干练且位于硬盘根目录等浅层目录。否则后续实验会出现路径超出Windows系统支持的最大长度，一旦出现就需要将之前所有的IP一一打开全部重新生成重新归置

4. 将整学期实验的所有IP核组织到统一的浅目录中，后续所有实验的IP Catalog都引用该目录下的IP

5. 在修改某些IP时，采用右键IP，选择`Edit in IP Packager`进入自动生成的子工程中修改，修改完成后`Repackage IP`，然后回到父工程中`Upgrade IP`即可。可以避免自认为IP更新了，实际上工程并没识别到

6. 在进行`Generate Output Product`时，尽量从头到尾均选择`Out of Context`模式。可以便于仿真

7. Slide中存在一定的错误，请认真阅读，结合理论课程知识分析，只要理论课认真学习，完全可以辨识和解决

8. Tools → Setting中可以开启一些设置方便实验

   - Source File → File Saving中可以开启自动保存源文件，减少弹窗询问是否保存
   - Dispaly → Spacing → Density调整为Compact可以在小屏幕上显示更多内容
   - Text Editor中可以调整编辑器为其他第三方编辑器 如VS Code
   - Text Editor → Fonts and Colors可以调整Fonts family和size，原始的太费眼睛了