# Simple-arch-install-guide

## BIOS + MBR + LVM on LUKS

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

+250M
n
p

a
1
w
```
creates a 250m boot partition (needs to be separate bc root encryption) and the rest of the disk for data

formatting partitions
```
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda2
```

### Encrypting stuff
```
cryptsetup luksFormat --type luks2 /dev/sda2
cryptsetup open /dev/sda2 cryptlvm
pvcreate /dev/mapper/cryptlvm
vgcreate MyVolGroup /dev/mapper/cryptlvm

lvcreate -L 8G MyVolGroup -n swap
lvcreate -L 32G MyVolGroup -n root
lvcreate -l 100%FREE MyVolGroup -n home

mkfs.ext4 /dev/MyVolGroup/root
mkfs.ext4 /dev/MyVolGroup/home
mkswap /dev/MyVolGroup/swap
```
### Mounting stuff
```
mount /dev/MyVolGroup/root /mnt
mkdir /mnt/home
mount /dev/MyVolGroup/home /mnt/home
swapon /dev/MyVolGroup/swap
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
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

nvim /etc/locale.conf
type: LANG=en_US.UTF-8

nvim /etc/vconsole.conf
type: KEYMAP=de-latin1
```

### set hostname
```
nvim /etc/hostname
```

### generate initramfs
```
mkinitcpio -p linux
nvim /etc/mkinitcpio.conf 
change to: HOOKS=(base udev autodetect keyboard keymap consolefont modconf block encrypt lvm2 filesystems fsck)
```

### set root pw
```
passwd
```

### install bootloader
```
pacman -S grub
grub-install --target=i386-pc /dev/sda
nvim /etc/default/grub
change to: GRIB_CMDLINE_LINUX"cryptdevice=UUID=<actual uuid of /dev/sda2(encytped partition) get it from blkid>:cryptlvm root=/dev/MyVolGroup/root
```
after this there is some magic because lvm is currently buggy and config generation will hang so
```
exit
mkdir /mnt/hostrun
mount --bind /run /mnt/hostrun
arch-chroot /mnt
mkdir /run/lvm
mount --bind /hostrun/lvm /run/lvm
```
and then 
```
grub-mkconfig -o /boot/grub/grub.cfg
```

### Reboot
and you're done
