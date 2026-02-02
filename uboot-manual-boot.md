```
setenv bootargs "console=ttyAMA0,115200 console=tty1 earlycon=pl011,0x3f201000,115200n8 root=/dev/mmcblk0p2 rw rootwait rootfstype=ext4 init=/bin/sh rdinit=/bin/sh loglevel=7 debug ignore_loglevel 8250.nr_uarts=2"
fatload mmc 0:1 ${kernel_addr_r} Image
fatload mmc 0:1 ${fdt_addr_r} bcm2711-rpi-4-b.dtb
booti ${kernel_addr_r} - ${fdt_addr_r}
```
