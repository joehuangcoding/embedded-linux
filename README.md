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


## Building Cross Compiler for Rasperrypi 4
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


```


# Bootloader 

# Kernel 

# Root filesystem 




# Book repo
https://github.com/PacktPublishing/Mastering-Embedded-Linux-Programming-Third-Edition
