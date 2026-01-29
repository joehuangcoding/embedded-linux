# Dependencies
```
sudo apt install -y git dosfstools gcc-aarch64-linux-gnu make flex bison bc libssl-dev
```

# Build u-boot 
```
rm -rf boot

# clone a recent release of u-boot
git clone --depth=1 --branch=v2023.01 https://source.denx.de/u-boot/u-boot.git

# cross compile for 64 bit arm architecture using RPi4 config
cd u-boot/
CROSS_COMPILE=aarch64-linux-gnu- make rpi_4_defconfig
CROSS_COMPILE=aarch64-linux-gnu- make -j6
cd ..
mkdir boot
cp u-boot/u-boot.bin boot/
```

# Get the linux Img and some boot files from the pi repo
```
# clone a recent release of the Raspberry Pi firmware
git clone --depth=1 --branch=1.20230106 https://github.com/raspberrypi/firmware.git

mkdir -p boot/overlays
cp firmware/boot/{bcm2711-rpi-4-b.dtb,fixup4.dat,start4.elf} boot/
cp firmware/boot/overlays/disable-bt.dtbo boot/overlays/
zcat -S .img firmware/boot/kernel8.img > boot/Image
```

# Root File System
```
wget https://dl-cdn.alpinelinux.org/alpine/v3.17/releases/aarch64/alpine-minirootfs-3.17.1-aarch64.tar.gz
mkdir rootfs
tar xf alpine-minirootfs-*-aarch64.tar.gz -C rootfs
# create initramfs
cd rootfs && find . | fakeroot  cpio -ov -H newc | gzip > ../initramfs.igz && cd ..
```
# /sys/kernel/debug/gpio lives on debugfs, which is not mounted by default, especially in an initramfs.
```
mount -t debugfs debugfs /sys/kernel/debug
cat /sys/kernel/debug/gpio | grep gpio-17
```

```
rm -rf boot
mkdir -p boot/overlays
cp u-boot/u-boot.bin boot/
cp firmware/boot/{bcm2711-rpi-4-b.dtb,fixup4.dat,start4.elf} boot/
cp firmware/boot/overlays/disable-bt.dtbo boot/overlays/

# uncompress kernel (U-Boot can boot compressed but takes special handling)
zcat -S .img firmware/boot/kernel8.img > boot/Image

# use the mkimage tool to prepare the initramfs and boot script for u-boot
./u-boot/tools/mkimage -A arm64 -O linux -T script -C none -d boot.txt boot/boot.scr
./u-boot/tools/mkimage -A arm64 -O linux -T ramdisk -d initramfs.igz boot/uRamdisk

# create a simple config.txt file
cat << EOF > boot/config.txt
# Disable bluetooth so we can user serial console
dtoverlay=disable-bt
# Run in 64-bit mode
arm_64bit=1
# Use Das U-Boot
kernel=u-boot.bin
EOF
```


# Set low
```
echo 0 > /sys/class/gpio/gpio17/value
cat /sys/kernel/debug/gpio | grep gpio-17
 gpio-17  (GPIO17              |sysfs               ) out lo
```

# Set high
```
echo 1 > /sys/class/gpio/gpio17/value
cat /sys/kernel/debug/gpio | grep gpio-17
 gpio-17  (GPIO17              |sysfs               ) out hi
```
