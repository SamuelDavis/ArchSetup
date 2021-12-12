# Initial Install

#### Synchronize network time protocol
```shell
timedatectl set-ntp true
```

#### Optimize Package Manager
```shell
sudo pacman -Syy reflector
reflector \
  --country 'United States' \
  --age 6 \
  --sort rate \
  --save /etc/pacman.d/mirrorlist
```

> Partitioning and Formatting vary wildly depending on what kind of system,
> whether it's UEFI, etc...
>
> For a UEFI system, (eg. configured in VirtualBox)...
#### Partitioning
> /dev/sda1 is the Boot partition
> /dev/sda2 is the MacOS partition
```shell
gdisk /dev/sda3
```
> 1. n to create a new partition
> 1. 1 (default) for first partition
> 1. accept default partition starting position
> 1. +200M size for EFI partition
> 1. ef00 for 'EFI system partition
> 1. n to create a new partition
> 1. 2 (default) for second partition
> 1. accept default partition starting position
> 1. accept default partition ending position
> 1. accept default Linux Filesystem partition type
> 1. w to persist the partition table

#### Filesystem Formatting & Mounting
```shell
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2

mount /dev/sda2 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
```

#### Bootstrapping
> To really limit the potential for swapfile problems,
> MEMORY_BLOCK_SIZE * MEMORY_BLOCK_COUNT ~= the total amount of RAM on the machine
```shell
# bootstrap Arch
pacstrap /mnt base linux linux-firmware vim
# create filesystem table
genfstab -U /mnt >> /mnt/etc/fstab
# access new Arch installation
arch-chroot /mnt
```

#### Swapfile
```shell
MEMORY_BLOCK_SIZE="1G"
MEMORY_BLOCK_COUNT=8

dd \
  if=/dev/zero \
  of=/swapfile \
  bs=$MEMORY_BLOCK_SIZE \
  count=$MEMORY_BLOCK_COUNT \
  status=progress
chmod 600 /swapfil
mkswap /swapfile
swapon /swapfile
echo "/swapfile none defaults 0 0" >> /etc/fstab
```

#### Timezone Configuration
```shell
LANG="en_US.UTF-8"
LOCALE="${LANG} UTF-8"

echo $LOCALE >> /etc/locale.gen
locale-gen
echo "LANG=${LANG}" >> /etc/locale.conf
```

#### Host Config
```shell
HOSTNAME="arch"

echo $HOSTNAME >> /etc/hostname
echo -e "127.0.0.1\tlocalhost" >> /etc/hosts
echo -e "::1\t\tlocalhost" >> /etc/hosts
echo -e "127.0.1.1\t${HOSTNAME}.localdomain\t${HOSTNAME}" >> /etc/hosts
```

#### Root Password
```shell
passwd
```

#### Bootloader
```shell
# bootloading
pacman -S grub efibootmgr os-prober
# networking
pacman -S networkmanager network-manager-applet
# FAT filesystems
pacman -S mtools dosfstools
# development
pacman -S base-devel linux-headers
# package management
pacman -S reflector
# tooling
pacman -S openssh git xdg-utils xdg-user-dirs dialog
# virtualbox helper
pacman -S virtualbox-guest-utils

grub-install \
  --target=x86_64-efi \
  --efi-directory=/boot/efi \
  --bootloader-id=GRUB
grub-mkconfig -o /bootgrub/grub.cfg
```

#### Enable Background Services
```shell
# networking / internet
systemctl enable NetworkManager
# ssh
systemctl enable sshd
```

#### Add User
```shell
USERNAME="defaultuser"

useradd -mG wheel $USERNAME
passwd $USERNAME

EDITOR=vim visudo
```
> 1. to enable sudo for wheel users, uncomment %wheel ALL=(ALL) ALL

#### Finish
```shell
# log out of arch installation
exit
# unmount partitions
umount -a
# restart
reboot
```
