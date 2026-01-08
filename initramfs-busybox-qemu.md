# Build Linux with initramfs enable to load CPIO archive
```
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.6.16.tar.xz
tar -xvf linux-6.6.16.tar.xz
cd linux-6.6.16

# Config in macOS running ubuntu using UTM

make ARCH=arm64 defconfig
# Optional: Enable more features if needed
# Look for Initramfs options
make ARCH=arm64 menuconfig  

make ARCH=arm64 -j$(nproc) Image
```

# Build rootfilesystem with busybox-static

```
# Install busybox-static if not already done
sudo apt update
sudo apt install busybox-static

# Create basic folder, files
mkdir -p initramfs/{bin,sbin,dev,proc,sys,tmp,etc,usr/{bin,sbin}}

# Copy busybox binary into the root filesystem
cp /bin/busybox initramfs/bin/
chmod +x initramfs/bin/busybox

# Create all BusyBox applets (including /bin/sh)
cd initramfs/bin
./busybox --install -s .
cd -

# Create the init file
# The init file is the first program run by the kernel after it loads
cat > initramfs/init << 'EOF'
#!/bin/busybox sh

# Force creation of all applets/symlinks early
/bin/busybox --install -s /bin

# Mount filesystems (now mount, mkdir, etc. exist)
/bin/busybox mount -t proc     proc     /proc
/bin/busybox mount -t sysfs    sysfs    /sys
/bin/busybox mount -t devtmpfs devtmpfs /dev
/bin/busybox mount -t tmpfs    tmpfs    /tmp
/bin/busybox mkdir -p /run
/bin/busybox mount -t tmpfs   tmpfs    /run

# Welcome banner
/bin/busybox clear 2>/dev/null || true
/bin/busybox echo
/bin/busybox echo "========================================"
/bin/busybox echo "  Minimal Static Initramfs (ARM64)"
/bin/busybox echo "  BusyBox static shell running!"
/bin/busybox echo "  Type 'help' for available commands"
/bin/busybox echo "========================================"
/bin/busybox echo

# Drop into shell using BusyBox directly (bypasses missing /bin/sh)
/bin/busybox sh
EOF

```

# Output cpio.gz
```
cd initramfs
find . -print0 \
 | cpio --null -ov --format=newc \
 | gzip -9 > ../my_initramfs.cpio.gz
cd -

```

# Running qemu
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

