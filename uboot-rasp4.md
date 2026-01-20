# Useful resources 
https://stackoverflow.com/questions/64014958/cant-get-u-boot-running-on-raspberry-pi-4b-what-are-the-absolute-necessities

# Serial output 
- Get a raspberry debug probe or 3.3v usb uart
- Install https://teratermproject.github.io/index-en.html

# Dependencies
```
sudo apt install -y git dosfstools gcc-aarch64-linux-gnu make flex bison bc libssl-dev

https://docs.u-boot.org/en/stable/build/gcc.html
```

# Build
```
git clone --depth=1 --branch=v2026.01 https://source.denx.de/u-boot/u-boot.git
```

## Cross compile for 64 bit arm architecture RPi4
```
cd u-boot/
CROSS_COMPILE=aarch64-linux-gnu- make rpi_4_defconfig
CROSS_COMPILE=aarch64-linux-gnu- make -j6

# Copy the output 
cd ..
mkdir boot
cp u-boot/u-boot.bin boot/
```

# Copy raspberry 4 boot files
```
git clone --depth=1 --branch=1.20250915 https://github.com/raspberrypi/firmware.git
cp firmware/boot/{bcm2711-rpi-4-b.dtb,fixup4.dat,start4.elf} boot/

```
# Create raspi4 boot config file
```
cat << EOF > boot/config.txt
# Enable mini UART
enable_uart=1
# Run in 64-bit mode
arm_64bit=1
# Use Das U-Boot
kernel=u-boot.bin
EOF
```



# Manual steps to boot linux
```
U-Boot> setenv bootargs coherent_pool=1M 8250.nr_uarts=1 snd_bcm2835.enable_compat_alsa=0 snd_bcm2835.enable_hdmi=1 snd_bcm2835.enable_headphones=1 smsc95xx.macaddr=DC:A6:32:05:53:B6 vc_mem.mem_base=0x3ec00000 vc_mem.mem_size=0x40000000 console=ttyS0,115200 console=tty1 root=PARTUUID=7f586f18-02 rootfstype=ext4 elevator=deadline rootwait

U-Boot> load mmc 0:1 $fdt_addr_r bcm2711-rpi-4-b.dtb                 
47471 bytes read in 26 ms (1.7 MiB/s)

U-Boot> load mmc 0:1 $kernel_addr_r kernel8.img
15483392 bytes read in 823 ms (17.9 MiB/s)

load mmc 0:1 $ramdisk_addr_r uRamdisk

booti $kernel_addr_r - $ramdisk_addr_r - $fdt_addr_r
```




















