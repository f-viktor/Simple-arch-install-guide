# Simple-arch-install-guide

# UEFI

# step 0 : if you use something other than "en" keyboard layout
to set keyboard layout use loadkeys en

# step 1 : get your disks
fdisk -l  
lsblk  

# step 2 : partition your drives
cfdisk /dev/sdx -z  
 
set to gpt  
create 300M EFI system partition  
create RAMx2 partition as Linux SWAP  
partition rest as desired  
no need for boot flags 

# setp 3 : format your partitions
mkfs.ext4 /dev/sdx3   (this is the partition of /)  
mkfs.fat -F32 /dev/sdx2  (this is the EFI partition)  
mkswap /dev/sdx1 (this is your swap)  

# step 4 : mount your root drive
mount /dev/sdx3 /mnt
swapon /dev/sdx1
ddif you are using uefi also mount uefi into /mnt/boot or wherever you want it

# step 5 : set swap
swapon /dev/sdx1

# if you are not using a cable network set up a wifi connection via wifi-menu
(validate with ping)
before you reboot and you're on a wifi connection make sure you install "dialog" and "wpa_supplicant" 
try dhclient if you dont get an IP

# step 6 : pacstrap
pacstrap /mnt base base-devel


# step 7 : generate fstab
genfstab /mnt >> /mnt/etc/fstab

# step 8 : chroot
arch-chroot /mnt

# step 9 : set locale  
nano /etc/locale.gen
uncomment your locale
locale-gen
ln -s /usr/share/zoneinfo/place/of/locale /etc/localtime
echo "whateverhostname" > /etc/hostname
hwclock --systohc --utc

# step 10 : gset up users
passwd
useradd user -G wheel
su user
passwd user
sudo pacman -S sudo
visudo

# step 11 : bootloader
pacman -S grub efibootmgr dosfstools os-prober mtools
mkdir /boot/EFI
mount /dev/sda2 /boot/EFI  (this is your 300M EFI partition)
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
grub-mkconfig -o /boot/grub/grub.cfg

# step 12 : cleanup
umount /mnt

# step 13 : act like that one guy from V for vendetta
telinit 6



