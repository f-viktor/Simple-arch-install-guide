# what to do after first boot to get a graphical environment

## set language again
```
loadkeys de-latin
```

## add your user
```
useradd myname
mkdir /home/myname
chown myname:myname myname
passwd myname
```

## add user to sudoers

uncomment the %wheel line in sudoers
```
nano /etc/sudoers
usermod -aG wheel myname
```

## set up internet
```
sudo dhcpcd
```

## set up ntp
```
sudo timedatectl set-ntp true
```

## install xorg based i3 packages
```
sudo pacman -S xorg xorg-xinit i3 i3blocks rofi xfce4-terminal
```

## install bonus packages
```
sudo pacman -S pulseaudio pavucontrol firefox neovim
```

## tell xinit to start i3
```
echo "exec i3" >> /home/pepe/.xinitrc
```

## reboot or exit your console, if root is still logged in X will not start
```
exit
```

## start your gui
```
startx
```
