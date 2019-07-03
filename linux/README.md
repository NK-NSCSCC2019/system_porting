<!-- TOC -->

- [关于编译出的内核版本](#关于编译出的内核版本)
    - [vmlinux](#vmlinux)
    - [vmlinux-1.0(目前只是个.config文件)](#vmlinux-10目前只是个config文件)
        - [General SetUp](#general-setup)
            - [Automatically append version information to the version string](#automatically-append-version-information-to-the-version-string)
            - [RCU Implementation](#rcu-implementation)
            - [enable deprecated sysfs features which may confuse old userspace](#enable-deprecated-sysfs-features-which-may-confuse-old-userspace)
            - [Support initial ramdisks compressed using](#support-initial-ramdisks-compressed-using)
            - [Configure standard kernel features(for small systems)](#configure-standard-kernel-featuresfor-small-systems)
            - [书上没有提及,保持默认的选项](#书上没有提及保持默认的选项)
        - [block layer](#block-layer)
        - [Machine selection](#machine-selection)
        - [Power Management support](#power-management-support)
        - [Bus options](#bus-options)
        - [Execytable file formats](#execytable-file-formats)
        - [Networking support](#networking-support)
        - [Device Drivers](#device-drivers)
        - [File systems](#file-systems)
            - [dnotify & inotify](#dnotify--inotify)
            - [Miscellaneous filesystems](#miscellaneous-filesystems)
        - [other](#other)

<!-- /TOC -->
# 关于编译出的内核版本

记录该文件夹下各个版本Linux内核的信息,方便后面出现bug的时候能够快速定位到问题所在.
> 使用make menuconfig对内核进行选择编译

## vmlinux

linux-2.6.32原版,未经过任何操作
>但是怀疑这个原版是被龙芯修改过了

## vmlinux-1.0(目前只是个.config文件)

基本按照鸟哥的Linux私房菜书中的教程来编译的,记录一些不一样的选项.
>书上提到但是make menuconfig中没有的不记录,默认不影响.书上没有的全部保持默认选项,后面不再提起.
书中提到的Processor type and features在make menconfig中没有找到.

### General SetUp

#### Automatically append version information to the version string

没有选择,觉得没有必要

#### RCU Implementation

只有唯一一个Tree-based hirearchical RCU可选,其他关于RCU的选项都保持默认选项(默认都未勾选)

#### enable deprecated sysfs features which may confuse old userspace

未勾选
>(翻译)启用已弃用的sysfs功能，这可能会混淆旧用户空间

#### Support initial ramdisks compressed using

gzip/bzip2/LZMA,选择了bzip2,书上说压缩效果好
>貌似可以多选

#### Configure standard kernel features(for small systems)

关于嵌入式系统的,子选项全部取消,但是外面的没法取消(仍有个*号,不知道是否影响)

#### 书上没有提及,保持默认的选项

Built-in initramfs compression mode(None)
embed ramdisk
Kernel Performance Events And Counters
Enable VM event counters for /proc/vmstat

>以上按照在make menuconfig中出现的先后顺序排列

### block layer

**Default I/O scheduler**选择了CFQ，关于这部分的选择可以参考博客[Linux的磁盘调度策略](http://blog.itpub.net/27425054/viewspace-768224/)

### Machine selection

System type:Loongson 1X family of machines
Machine Type:Loongson LS232 development board
>应该是龙芯对于Linux内核的修改

### Power Management support

make menuconfig只有5个选项,和书上一大堆选项不符,保持默认

### Bus options

只有一个PCI选项,没有选择

### Execytable file formats

保持默认选项

### Networking support

只有Network options可选,全部删去.暂时没有实现这部分功能的想法

### Device Drivers

勾选了Connetor - unified userspace <-> kernelspace linker(与用户/内核层级的信息沟通有关,不过可能针对大赛不用选择?)
取消了Memory Technology Device(MTD) support(对闪存,U盘的支持)
Real Time Clock勾选,不是选为模块
去掉了SCSI device support
Network device support只保留了一个和龙芯相关的
保留了SPI(serial peripheral interface,串口外设接口)
删除MMC/SD/SDIO card support
>关于剩下的部分,维持了默认选项(实在太多了...)

### File systems

>这部分内容比较诡异,感觉大概率会出错

选择了ext3作为文件系统,根据书进行勾选

#### dnotify & inotify

监控文件夹,用来支持热插拔,删除(可能出错)

#### Miscellaneous filesystems

保留默认值,无改动

### other

保留默认值,无改动
