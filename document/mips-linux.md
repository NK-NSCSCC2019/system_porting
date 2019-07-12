<!-- TOC -->

- [MIPS Linux 移植](#mips-linux-移植)
    - [Makefile/mipsel-linux-gcc 语法](#makefilemipsel-linux-gcc-语法)
        - [Makefile](#makefile)
            - [wildcard](#wildcard)
        - [objcopy](#objcopy)
        - [objdump](#objdump)
        - [gcc](#gcc)
        - [.lds](#lds)
    - [汇编和反汇编不是互为逆过程](#汇编和反汇编不是互为逆过程)
    - [vivado IP核](#vivado-ip核)
        - [inst_sram_addr](#inst_sram_addr)
        - [block memory](#block-memory)
    - [verilog语法](#verilog语法)
        - [`ifdef、`else、`endif、`define](#ifdefelseendifdefine)
        - [module #()](#module-)
        - [generate](#generate)

<!-- /TOC -->
# MIPS Linux 移植

记录移植Linux过程中新学习到的知识

## Makefile/mipsel-linux-gcc 语法

该部分参考龙芯的Makefile文件,主要的代码如下:

```Makefile
main.bin:main.elf
	${CROSS_COMPILE}objcopy -O binary -j .text $< $@ 

main.data:main.elf
	${CROSS_COMPILE}objcopy -O binary -j .data $< $@ 

main.elf: start.o libinst.a 
	${CROSS_COMPILE}gcc -E -P -Umips -D_LOADER -U_MAIN $(CFLAGS) bin.lds.S -o bin.lds
	${CROSS_COMPILE}ld -g -T  bin.lds  -o $@ start.o -L . -linst
	${CROSS_COMPILE}objdump -alD $@ > test.s
```

### Makefile

\$@ 目标文件，\$^ 所有的依赖文件，\$< 第一个依赖文件
**切记命令前面要用tab,直接复制命令可能会出错!**

#### wildcard

\$(wildcard \$(FILE))的意思是当前路径下的文件名匹配FILE的文件展开。假设当前路径下存在a.c 和 b.c,那么执行src=$(wildcard *.c),src的值就为a.c b.c；如果不使用通配符，比如src=$(wildcard c.c)；那么就是要展开当前路径下，文件名为c.c的文件，因为当前路径下文件不存在，因此src为空字符串。

### objcopy

可以将目标文件拷贝成和原来的文件不一样的格式
-O 指定输出文件类型
-j 拷贝指定的段落

### objdump

反汇编目标文件或者可执行文件的命令
-a 显示档案库的成员信息,类似ls -l将lib*.a的信息列出
-l 用文件名和行号标注相应的目标代码
-d 从objfile中反汇编机器码的可执行部分(.test?)
-D 与-d类似,但反汇编所有的section(.data .test)
-S 将代码段反汇编的同时，将反汇编代码和源代码交替显示
-D --no-show-raw-insn 不显示汇编指令的机器码
--prefix-addresses 打印每行的完整地址

### gcc

-E 只进行预处理,不进行编译,汇编,链接
-P 禁止在预处理器的输出中生成线标记。 当在非C代码上运行预处理器时，这可能很有用，并且会被发送到可能被行标记混淆的程序。
>(原文)Inhibit generation of linemarkers in the output from the preprocessor.  This might be useful when running the preprocessor on something that is not C code, and will be sent to a program which might be confused by the linemarkers.

### .lds

链接脚本文件,暂时没去深入了解

## 汇编和反汇编不是互为逆过程

生成的test.s带有太多冗余信息,无法编译
汇编和反汇编仅仅只是理论上可能互为逆过程,实际过程中很难达到,参考如下:
[objdump -D 输出的东西可以经修改后再编译吗？](https://www.zhihu.com/question/51050736)
[Trying to assemble the output of an disassembler (such as objdump) [duplicate]](https://stackoverflow.com/questions/8510129/trying-to-assemble-the-output-of-an-disassembler-such-as-objdump)
[How to disassemble, modify and then reassemble a Linux executable?](https://stackoverflow.com/questions/4309771/how-to-disassemble-modify-and-then-reassemble-a-linux-executable)

## vivado IP核

### inst_sram_addr

龙芯给出的inst_sram ip核只接受17位地址,即cpu_inst_addr[19:2],后面如果inst_sram还是放不下Linux内核,可以尝试修改inst_sram的ip核,也许可以扩大容量.
>/home/lin/loongson/lab/func_test_v0.01/soc_sram_func/run_vivado/mycpu_prj1/mycpu.ip_user_files/ip/inst_ram

目前已将测试的工程文件修改了

### block memory

尝试以后发现,vivado给的block memory并不像显示的那样write depth能达到1048576,试了几次发现最大只能到达373760(365×1024)

## verilog语法

### `ifdef、`else、`endif、`define

条件编译指令,类似C++和makefile中的用法

### module #()

可以提供外部用的常熟参数

### generate

 generate语句允许细化时间（Elaboration-time）的选取或者某些语句的重复。这些语句可以包括模块实例引用的语句、连续赋值语句、always语句、initial语句和门级实例引用语句等。细化时间是指仿真开始前的一个阶段，此时所有的设计模块已经被链接到一起，并完成层次的引用。
 >不是很懂
