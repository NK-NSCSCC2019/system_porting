# 杂项

## inst_sram_addr

龙芯给出的inst_sram ip核只接受17位地址,即cpu_inst_addr[19:2],后面如果inst_sram还是放不下Linux内核,可以尝试修改inst_sram的ip核,也许可以扩大容量.
>/home/lin/loongson/lab/func_test_v0.01/soc_sram_func/run_vivado/mycpu_prj1/mycpu.ip_user_files/ip/inst_ram

目前已将测试的工程文件修改了

## block memory

尝试以后发现,vivado给的block memory并不像显示的那样write depth能达到1048576,试了几次发现最大只能到达373760(365×1024)

## DEBUG

trace_ref = $fopen(`TRACE_REF_FILE, "w");
导致文件指针异常,无法正常表示debug_wb_pc等东西
