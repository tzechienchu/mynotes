# Chromebook install GUI Desktop

``` sh
sudo apt update -y
sudo apt dist-upgrade -y

sudo apt install task-lxde-desktop -y
or
sudo apt install task-kde-desktop -y

sudo apt install xserver-xephyr -y
sud apt install nano -y
sudo systemctl disable lightdm

nano /usr/bin/gol
Xephyr -br -fullscreen -resizeable :20 &
sleep 2
DISPLAY=:20 startlxde &
```
