<!-- TOC -->

- [README](#readme)
    - [document](#document)
    - [linux](#linux)
    - [serial_interface](#serial_interface)

<!-- /TOC -->
# README

仓库里主要是一些系统移植过程中记录的文档,以及收集/生成的一些文件.
本文档主要是记录仓库中各个文件和文档的作用.

## document

保存文档的目录.

|Name    |Function|
| ------ | ------ |
|picture|该文件夹存放文档需要的图片|
|mips_architecture.md|简单记录查阅MIPS32手册时的一些体会|
|mips_linux.md|记录移植Linux过程中学习到的一些新知识|
|some.md|平时写代码时的一些心得,错误记录,方便debug|

## linux

将Linux内核生成inst_ram.coe,data_ram.coe,具体实现方法自行阅读Makefile,文件夹内的READE.md记录了不同版本的Linux内核之间的差异.

## serial_interface

从[fpga4fun](https://www.fpga4fun.com/)网站获取的串口代码
