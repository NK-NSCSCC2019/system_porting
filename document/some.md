# 杂项

龙芯给出的inst_sram ip核只接受17位地址,即cpu_inst_addr[19:2],后面如果inst_sram还是放不下Linux内核,可以尝试修改inst_sram的ip核,也许可以扩大容量.
>/home/lin/loongson/lab/func_test_v0.01/soc_sram_func/run_vivado/mycpu_prj1/mycpu.ip_user_files/ip/inst_ram
