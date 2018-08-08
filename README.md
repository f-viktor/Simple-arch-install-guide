# Simple-arch-install-guide

# step 1 : get your disks
fdisk -l
lsblk

# step 2 : partition your drives
cfdisk /dev/sdx
if you are using MBR set bootable flag
if you are using uefi, set a fat32 formatted 256-512 MB partition set as EFI
if you want to set a swap, set it as Linux swap

# setp 3 : format your partitions
mkfs.ext4 /dev/sdx1   
this is the partition of /

# step 4 : mount your root drive
mount /dev/sdx1 /mnt
if you are using uefi also mount uefi into /mnt/boot or wherever you want it

# step 5 : set swap
mkswap /dev/sdx2
swapon /dev/sdx2

# if you are not using a cable network set up a wifi connection via wifi-menu
(validate with ping)

# step 6 : pacstrap
pacstrap /mnt base base-devel

# step 7 : chroot
arch-chroot /mnt

# step 8 : set locale  
nano /etc/locale.gen
uncomment your locale
locale-gen
ln -s /usr/share/zoneinfo/place/of/locale /etc/localtime
echo whateverhostname > /etc/hostname

# step 9 : bootloader
pacman -S grub-bios
grub-install /dev/sdx
if you are using uefi its gonna be more complicated tbh
mkinitpico -p linux
grub-mkconfig -o /boot/grub/grub.cfg
exit

# step 10 : generate fstab
genfstab /mnt >> /mnt/etc/fstab

# step 11 : cleanup
umount /mnt

# step 12 : reboot




