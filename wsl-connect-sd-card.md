
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
