export CROSS_COMPILE ?= mipsel-linux-

OBJDIR = ./obj
kernel = vmlinux-2.0

all:
	make compile

compile:main.bin main.data convert
	./convert
	${CROSS_COMPILE}objdump -D --no-show-raw-insn ${kernel} > test.s
	mkdir -p $(OBJDIR)
	mv test.s    $(OBJDIR)/.
	mv main.bin  $(OBJDIR)/.
	mv main.data $(OBJDIR)/.
	mv *.coe     $(OBJDIR)/.
	mv *.mif     $(OBJDIR)/.

main.bin:${kernel}
	${CROSS_COMPILE}objcopy -O binary -j .text $< $@ 

main.data:${kernel}
	${CROSS_COMPILE}objcopy -O binary -j .data $< $@ 

convert:convert.c
	gcc  $(ALIGNED) -o convert  convert.c

clean:
	rm -f *.o *.a 
	rm -rf obj
	rm -f *.coe *.mif
	rm -f *.bin *.data
	rm -f test.s

help:
	@echo "################################################################"
	@echo "### help for compiling func"
	@echo "################################################################"
	@echo "### options:"
	@echo "###     make      : get compiled result, which is saved in ./obj"
	@echo "###     make clean: remove *.o, *.a, ./obj, and failure result"
	@echo "###     make help : show help information"
	@echo "###############################################################"
