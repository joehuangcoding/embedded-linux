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

- recipes - python and shell script
- bitbake - the task scheduler


