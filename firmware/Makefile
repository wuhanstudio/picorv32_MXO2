#!/bin/bash
CROSS=riscv32-unknown-elf-
CFLAGS=

all: firmware.bin
	dd if=/dev/zero bs=4M count=1 of=bootloader.bin
	dd conv=notrunc bs=4M bs=1 if=firmware.bin of=bootloader.bin seek=1048576

upload: bootloader.bin
	sudo flashrom -p linux_spi:dev=/dev/spidev0.0,spispeed=2000 -l rom.layout -i bootloader -w bootloader.bin

firmware.bin: firmware.elf
	$(CROSS)objcopy -O binary firmware.elf firmware.bin

picorv32_sections.lds: sections.lds
	$(CROSS)cpp -P -DHX8KDEMO -o $@ $^

firmware.elf: picorv32_sections.lds start.s firmware.c
	$(CROSS)gcc $(CFLAGS) -DHX8KDEMO -march=rv32imc -Wl,-Bstatic,-T,picorv32_sections.lds,--strip-debug -ffreestanding -nostdlib -o firmware.elf start.s firmware.c

clean:
	rm -f picorv32_sections.lds *.bin *.elf
