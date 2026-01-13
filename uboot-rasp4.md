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
























