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

```
//Switch to root user
sudo -i
// Download CrossTool-NG
sudo git clone https://github.com/crosstool-ng/crosstool-ng.git
git checkout crosstool-ng-1.24.0
./bootstrap
// The --prefix=${PWD} option means that the program will be installed into the current directory
./configure --prefix=${PWD}
make
make install
```

# Bootloader 

# Kernel 

# Root filesystem 




# Book repo
https://github.com/PacktPublishing/Mastering-Embedded-Linux-Programming-Third-Edition
