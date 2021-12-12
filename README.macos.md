# MacOS Differences

> Partitioning and Formatting vary wildly depending on what kind of system,
> whether it's UEFI, etc...
>
> For a Dual-Booting MacOS, you can split the harddrive using the Mac tools...
#### Partitioning
> /dev/sda1 is the Boot partition
> /dev/sda2 is the MacOS partition
```shell
gdisk /dev/sda3
```
> 1. n to create a new partition
> 1. 1 (default) for first partition
> 1. accept default partition starting position
> 1. accept default partition ending position
> 1. accept default Linux Filesystem partition type
> 1. w to persist the partition table

#### Filesystem Formatting & Mounting
> The Bootloader is already available from MacOS
```shell
mkfs.ext4 /dev/sda3

mount /dev/sda3 /mnt
mkdir -p /mnt/boot
mount /dev/sda1 /mnt/boot
```
