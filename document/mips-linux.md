<!-- TOC -->

- [MIPS Linux 移植](#mips-linux-移植)
    - [Makefile/mipsel-linux-gcc 语法](#makefilemipsel-linux-gcc-语法)
        - [Makefile](#makefile)
            - [wildcard](#wildcard)
        - [objcopy](#objcopy)
        - [objdump](#objdump)
        - [gcc](#gcc)
        - [.lds](#lds)
    - [DONE](#done)
    - [problem](#problem)
        - [生成的coe文件过大](#生成的coe文件过大)
        - [反汇编有问题](#反汇编有问题)
        - [汇编和反汇编不是互为逆过程](#汇编和反汇编不是互为逆过程)

<!-- /TOC -->
# MIPS Linux 移植

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

## DONE

去除vmlinux格式,只保留汇编的代码段和数据段,以此来生成inst_ram.coe和data_ram.coe
将vmlinux反汇编成test.s

## problem

### 生成的coe文件过大

可以尝试把vmlinux反汇编成test.s,然后删掉一部分,再重新编译成coe
>尝试裁剪了内核,但是裁剪的内核反而比原来的还要大

### 反汇编有问题

```Makefile
${CROSS_COMPILE}objdump -alD vmlinux > test.s
```

这句无法停止,可能是因为结果文件太大,再加上-a编译选项时间过长

### 汇编和反汇编不是互为逆过程

生成的test.s带有太多冗余信息,无法编译
汇编和反汇编仅仅只是理论上可能互为逆过程,实际过程中很难达到,参考如下:
[objdump -D 输出的东西可以经修改后再编译吗？](https://www.zhihu.com/question/51050736)
[Trying to assemble the output of an disassembler (such as objdump) [duplicate]](https://stackoverflow.com/questions/8510129/trying-to-assemble-the-output-of-an-disassembler-such-as-objdump)
[How to disassemble, modify and then reassemble a Linux executable?](https://stackoverflow.com/questions/4309771/how-to-disassemble-modify-and-then-reassemble-a-linux-executable)
