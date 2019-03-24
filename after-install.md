# what to do after first boot to get a graphical environment

## set language again
```
loadkeys hu
```

## add your user
useradd myname
mkdir /home/pepe
chown pepe:pepe pepe
passwd pepe

## add user to sudoers

uncomment the %wheel line in sudoers
```
nano /etc/sudoers
usermod -aG wheel pepe
```

## set up internet
```
sudo dhcpcd
```

## install xorg based i3 packages
```
sp -S xorg xorg-xinit i3 i3blocks dmenu rxvt-unicode
sudo pacman -S xorg xorg-xinit i3 i3blocks dmenu rxvt-unicode
```

## tell xinit to start i3
```
echo "exec i3" >> /home/pepe/.xinitrc
```

## reboot or exit your console, if root is still logged in X will not start
```
reboot
```

## start your gui
```
startx
```