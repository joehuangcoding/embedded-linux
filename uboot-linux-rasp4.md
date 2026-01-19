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

```
