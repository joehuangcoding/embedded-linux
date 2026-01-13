# Serial output 
- Get a raspberry debug probe or 3.3v usb uart
- Install https://teratermproject.github.io/index-en.html

# Dependencies
```
sudo apt install -y git dosfstools gcc-aarch64-linux-gnu make flex bison bc libssl-dev

https://docs.u-boot.org/en/stable/build/gcc.html
```

# Build
git clone --depth=1 --branch=v2026.01 https://source.denx.de/u-boot/u-boot.git
## cross compile for 64 bit arm architecture RPi4
cd u-boot/
CROSS_COMPILE=aarch64-linux-gnu- make rpi_4_defconfig
CROSS_COMPILE=aarch64-linux-gnu- make -j6
