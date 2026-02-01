# Oracle VM VirtualBox
- VirtualBox version 7.0
- Download ubuntu iso
- Add and run the ubuntu system
- Read the SD card. You will need a SD Card Reader. VirtualBox doesn't seem to read from SD card directly
- Enable copy and paste
```
1. Install Virtual Box extension pack in host
https://download.virtualbox.org/virtualbox/7.0.20/Oracle_VM_VirtualBox_Extension_Pack-7.0.20.vbox-extpack
2. Activate shared clipboard in menu
https://askubuntu.com/a/438204/245048
3. Install Virtual Box guest extensions in ubuntu guest
sudo apt-get install virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11

or

sudo apt-get install virtualbox-guest-additions-iso

4. Reboot virtual machine
```

# Windows check the partitions
1. Get the DriveLetter

Get-Partition -DiskNumber 2 | Select-Object PartitionNumber, DriveLetter

2. Remove the drive letters (don’t delete data):

Replace "E:" with the actual drive letter of the FAT32 partition.

Do the same for any other mounted partitions (F:, etc.).

This does not delete partitions. It just makes them invisible to Windows, freeing the disk for WSL mounting.

Get-Partition -DiskNumber 2 | Remove-PartitionAccessPath -AccessPath "E:"


# usbip-wins
https://github.com/rpasek/usbip-wsl2-instructions



# Mounting fs to folder
```
sudo mount /dev/sdc2 ./rpi-root/
sudo cp -a ./rootfs/. ./rpi-root/
```
# Link init
```
sudo ln -sf /bin/busybox /sbin/init
```

# etc/inittab for busybox
```
sudo tee /mnt/rpi-root/etc/inittab > /dev/null << 'EOF'
::sysinit:/etc/init.d/rcS
# ::askfirst:-/bin/sh/
s0:12345:respawn:/sbin/getty -L ttyAMA0 115200 vt100
::ctrlaltdel:/bin/umount -a -r
::shutdown:/bin/umount -a -r
EOF
```

# Example init.d/rcS
```
sudo tee /etc/init.d/rcS > /dev/null << 'EOF'
#!/bin/sh

# Mount pseudo filesystems
mount -t proc proc /proc
mount -t sysfs sys /sys
mount -t devtmpfs dev /dev
mount -t tmpfs tmp /tmp

# Set up networking (optional)
# ifconfig lo up
# echo "nameserver 8.8.8.8" > /etc/resolv.conf
# Any other initialization commands go here
EOF
```
