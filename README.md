# Simple-arch-install-guide

## BIOS + MBR 

Sources.list:  
https://wiki.archlinux.org/index.php/Installation_guide  
https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS  
https://unix.stackexchange.com/questions/105389/arch-grub-asking-for-run-lvm-lvmetad-socket-on-a-non-lvm-disk  

### set keyboard layout
`loadkeys de-latin1`

### check if you are in MBR
```
ls /sys/firmware/efi/efivars
```
should return "not found"

### check/fix internet
```
ping archlinux.org
```
if that fails
```
systemctl start dhcpcd
```
if you are on wifi
```
wifi-menu
```

### set clock
```
timedatectl set-ntp true
```

### partitions:
Make sure you set the disk to MBR 
```
gdisk
/dev/sda
r
g
w
Y
```

create partitions 
```
d
d
d
d
fdisk /dev/sda
n
p
+8G

a
2
w
```
creates a 8G swap partition 

formatting partitions
```
mkswap /dev/sda1
mkfs.ext4 /dev/sda2
```

### Mounting stuff
```
mount /dev/sda2 /mnt
swapon /dev/sda1
```

### pacstrapping 
```
pacstrap /mnt base base-devel
```

### generate fstab
```
genfstab -U /mnt >> /mnt/etc/fstab
```

### put it in
```
arch-chroot /mnt
pacman -S neovim
```

### generate locale
```
nvim /etc/locale.gen
locale-gen
```

### set hostname
```
nvim /etc/hostname
```

### generate initramfs
```
mkinitcpio -p linux
```

### set root pw
```
passwd
```

### install bootloader
```
pacman -S grub
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

### Reboot
and you're done
