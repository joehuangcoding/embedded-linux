# embedded-linux
study study :)

# 4 Main Areas
1. Cross compiler tools. Build the compiler tools using crosstool-NG
2. RomCode in NorFlash (loading SPL)-> MLO (SPL) runs on SRAM (loading u-boot)-> u-boot run on up of DDRAM (TPL)-> u-boot (TPL) loading kernel into DDRAM
```
  a. MLO and u-boot.img are in the boot partition in the SD card for the beaglebone sample (similar to raspberry pi i guess)
```
3. Config and build kernel
4. Building a root filesystem 

# Cross Compiler

Dependencies:
```
sudo apt-get install autoconf automake bison bzip2 cmake \
flex g++ gawk gcc gettext git gperf help2man libncurses5-dev libstdc++6 libtool \ 
libtool-bin make patch python3-dev rsync texinfo unzip wget xz-utils
```


## Building Cross Compiler for raspberrypi 4
```
//Switch to root user and `su - <username>` switch back
sudo -i
sudo git clone https://github.com/crosstool-ng/crosstool-ng.git
cd crosstool-ng
./bootstrap
// The --prefix=${PWD} option means that the program will be installed into the current directory
./configure --prefix=${PWD}
make && make install

// Check Available ARM Targets
bin/ct-ng list-samples | grep arm
// ...
//arm-unknown-linux-gnueabi
//armv8-rpi4-linux-gnueabi
//armv8-rpi4-linux-gnueabihf

//This showing the default config
bin/ct-ng show-arm-cortex_a8-linux-gnueabi

//Review the configuration
bin/ct-ng arm-cortex_a8-linux-gnueabi

//Change the config
bin/ct-ng menuconfig

//Target Options:
//Target Architecture → arm
//Target CPU → cortex-a72
//Floating Point → hard
//Endianness → Little endian
//ABI → EABIHF

//Toolchain Options:
//Tuple's vendor string → rpi4

//Operating System:
//Choose Linux
//Enable Kernel Headers

//C Library:
//Choose glibc (or musl if you prefer a smaller toolchain)

bin/ct-ng distclean
bin/ct-ng --version
bin/ct-ng build //Take a half hour to build the cross-compiler



```
The built tool will be under ~/x-tools folder e.g. ~/x-tools/arm-cortex_a8-linux-gnueabihf.

Testing the cross-compiler

```
PATH=~/x-tools/arm-rpi4-linux-gnueabi/bin:$PATH
arm-rpi4-linux-gnueabi-gcc -v
arm-rpi4-linux-gnueabi-gcc --target-help

nano helloworld.c
#include <stdio.h>
#include <stdlib.h>
int main (int argc, char *argv[])
{
   printf ("Hello, world!\n");
   return 0;
}

arm-rpi4-linux-gnueabi-gcc helloworld.c -o helloworld
```
- Copy helloworld executable to the rasperry pi 4 and run it


# Bootloader 

# Kernel 

# Root filesystem 




# Book repo
https://github.com/PacktPublishing/Mastering-Embedded-Linux-Programming-Third-Edition



# Build System
1. Buildroot
2. Yocto Project

## Yocto
The Yocto Project is a more complex beast than Buildroot. Not only can it build toolchains, bootloaders, kernels, and root filesystems as Buildroot can, but it can generate an entire Linux distribution for you with binary packages that can be installed at runtime.

- recipes: python and shell script
- bitbake: the task scheduler
- OE-Core: core metadata
- Poky: This is the reference distribution.
- Documentation: developer's guides
- Toaster: a web-based interface to BitBake and its metadata.

### Installing Yocto
```
git clone -b dunfell git://git.yoctoproject.org/poky.git
```


### Configuration
`source poky/oe-init-build-env` creating the `/build` directory

source this script each time you want to work on this project



---

# kernal build with initramfs
- https://github.com/ntegan/basic_initramfs
- https://docs.kernel.org/filesystems/ramfs-rootfs-initramfs.html

# Experiment, busybox, initramfs, linux build, qemu and raspi4
## Linux build
```
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.6.16.tar.xz
tar -xvf linux-6.6.16.tar.xz
cd linux-6.6.16

# Config in macOS running ubuntu using UTM

make ARCH=arm64 defconfig
make ARCH=arm64 menuconfig  # Optional: Enable more features if needed
make ARCH=arm64 -j$(nproc) Image
```

## Root file system 
```
# Install busybox-static if not already done
sudo apt update
sudo apt install busybox-static
mkdir -p initramfs/{bin,sbin,dev,proc,sys,tmp,etc,usr/{bin,sbin}}
# Copy the static busybox and install symlinks correctly
cp /bin/busybox initramfs/bin/busybox
# This is the key line — installs symlinks cleanly into bin/ and sbin/
initramfs/bin/busybox --install -s initramfs/bin
# Create the init script
cat > initramfs/init << 'EOF'
#!/bin/busybox sh

# Mount essential filesystems
mount -t proc none /proc
mount -t sysfs none /sys
mount -t devtmpfs none /dev
mount -t tmpfs none /tmp

echo
echo "=================================="
echo "  Minimal Static Initramfs Ready! "
echo "  Running on ARM64 (aarch64)     "
echo "=================================="
echo

# Start an interactive shell
exec /bin/sh
EOF

# Make the init script executable
chmod +x initramfs/init

```

## Running qemu
```
qemu-system-aarch64 \
  -machine virt \
  -cpu cortex-a57 \
  -m 512 \
  -kernel /boot/vmlinuz-$(uname -r) \
  -initrd my_initramfs.cpio.gz \
  -nographic \
  -append "console=ttyAMA0 rdinit=/init"
```











