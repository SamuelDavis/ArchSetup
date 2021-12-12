# User Setup

#### Video Drivers
```shell
# determine graphics Subsystem
lspci -v | grep A1 -e VGA -e 3D
# search for video drivers for target Subsystem
pacman -Ss xf86-video | vim - -n
# install video drivers
pacman -S xf86-video-ati
```

#### Display Manager
```shell
sudo pacman -S xorg
cp /etc/X11/xinit/xinitrc ~/.xinitrc
```
> 1. delete "twm &"
> 1. change "exec xterm" to "exec dwm"
>
> add the following to ~/.bash_profile:
> ```shell
> if [ -z "${DISPLAY}" ] && [ "${XDG_VTNR}" -eq 1 ]; then
>   exec startx
> fi
> ```

#### User Interface
```shell
mkdir ~/aur && cd ~/aur
git clone https://git.suckless.org/dwm
git clone https://git.suckless.org/dmenu
git clone https://git.suckless.org/st
git clone https://aur.archlinux.org/brave-bin.git

cd ~/aur/dwm && sudo make clean install
cd ~/aur/dmenu && sudo make clean install
cd ~/aur/st && sudo make clean install
```

#### VIM
```shell
# support meta+shift+c/v in INSERT mode
echo "set clipboard=unnamed" >> ~/.vimrc
```
