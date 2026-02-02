```
cd ~
git clone --depth=1 https://github.com/raspberrypi/linux
cd linux

export ARCH=arm64
export CROSS_COMPILE=aarch64-linux-gnu-

make bcm2711_defconfig

//make menuconfig

make -j$(nproc)

// Created
arch/arm64/boot/Image
arch/arm64/boot/dts/broadcom/*.dtb


//Building kernel modules
make modules
#Boot partition FAT32
mkdir -p ~/rpi4/boot 

# rootfs ext4
mkdir -p ~/rpi4/rootfs 


make modules_install INSTALL_MOD_PATH=~/rpi4/rootfs

```
