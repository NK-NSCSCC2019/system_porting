# 杂项

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

## DEBUG

>testbench.v

trace_ref = $fopen(`TRACE_REF_FILE, "w");
导致文件指针异常,无法正常表示debug_wb_pc等东西

## idea

暂时考虑的是利用龙芯启动linux内核的方法--用串口和SPI Flash控制器往箱子上烧PMON,然后利用PMON的load指令,从本机的tftp服务器上,把Linux内核下载到Flash中,并利用PMON的指令启动.
因此需要CPU支持串口,网口,SPI
>但是MIPS要支持外设的话,需要实现虚拟地址,也就是需要TLB和MMU
并且关于外设的CP0寄存器,需要区别user Mode和kernel Mode,因此还需要实现一堆CP0寄存器

是否还需要考虑切换用户态和内核态的时候,保留一块内存用于存放重要寄存器的信息
同时还需要新加对更多异常的处理,比如cache,TLB缺失等情况

