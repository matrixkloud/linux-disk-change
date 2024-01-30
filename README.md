# linux-disk-change
Guide of changing primary disk , resize disk or downgrade disk

### === Process of Disk Change/migration ======
```
sudo parted /dev/vdb mklabel gpt
sudo parted /dev/vdb mkpart biosboot 1MiB 3MiB
sudo parted /dev/vdb set 1 bios_grub on
sudo parted /dev/vdb mkpart primary ext4 3MiB 100%
sudo mkfs.ext4 /dev/vdb2
fdisk -l
lsblk
sudo blkid -s UUID -o value /dev/vdb2
sudo mkdir /mnt/vda
sudo mkdir /mnt/vdb
sudo mount /dev/vda2 /mnt/vda
sudo mount /dev/vdb2 /mnt/vdb
```



### Copy all the data from primary disk to secondary disk 
```
cd /mnt/vdb
sudo rsync -avxHAX --progress /mnt/vda/ /mnt/vdb/
```
### ==== Bind Mount Some System Directories =======
```
sudo mount --bind /dev /mnt/vdb/dev
sudo mount --bind /proc /mnt/vdb/proc
sudo mount --bind /sys /mnt/vdb/sys
```

### ===== Chroot into the Target System ======
```
sudo chroot /mnt/vdb
```
### ===== Install GRUB on the Target Disk ======

# For BIOS Mode: 
```
grub-install --target=i386-pc --recheck /dev/vdb 
```
or 
# For EFI Mode: 
```
grub-install --recheck /dev/vdb
```

### ====== Generate GRUB Configuration ======
```
update-grub
```
### ===== Exit the chroot Environment =====
```
exit
```
### ===== Unmount Directories ==========

```
sudo umount /mnt/vdb/dev
sudo umount /mnt/vdb/proc
sudo umount /mnt/vdb/sys
```
