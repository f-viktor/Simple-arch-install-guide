# Simple-arch-install-guide

## BIOS + MBR + LVM on LUKS

Sources.list:  
https://wiki.archlinux.org/index.php/Installation_guide  
https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS  
https://unix.stackexchange.com/questions/105389/arch-grub-asking-for-run-lvm-lvmetad-socket-on-a-non-lvm-disk  

### formatting partitions:
Make sure you set the disk to MBR 
```
gdisk
/dev/sda
r
g
w
Y
```

