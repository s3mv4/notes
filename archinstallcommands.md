# Raw Arch Install Commands 
(see archinstallguide.md for the guide which actually explains what each command does).
## Inside the live environment
### Keyboard layout
```
# loadkeys dvorak-programmer
```
### Internet
```
# iwctl
```
```
[iwd]# station wlan0 connect SSID
```
### Partitioning the disks
```
# fdisk DISK
```
### Formatting the partitions
```
# mkfs.ext4 /dev/root_partition
```
```
# mkswap /dev/swap_partition
```
```
# mkfs.fat -F 32 /dev/efi_system_partition
```
### Mounting the filesystems
```
# mount /dev/root_partition /mnt
```
```
# mount --mkdir /dev/efi_system_partition /mnt/boot
```
```
# swapon /dev/swap_partition
```
### Installing essential packages
```
# pacstrap -K /mnt base base-devel linux linux-firmware intel-ucode efibootmgr networkmanager vim man sudo git
```
### Fstab
```
# genfstab -U /mnt >> /mnt/etc/fstab
```
### Chroot
```
# arch-chroot /mnt
```
### Time
```
# ln -sf /usr/share/zoneinfo/Europe/Amsterdam /etc/localtime
```
```
# hwclock --systohc
```
```
# timedatectl set-ntp true
```
### Localization
`/etc/locale.gen`, uncomment:
```
en_US.UTF-8 UTF-8
```
```
# locale-gen
```
`/etc/locale.conf`:
```
LANG=en_US.UTF-8
```
`/etc/vconsole.conf`:
```
KEYMAP=dvorak-programmer
```
### Network configuration
`/etc/hostname`:
```
archlinux
```
```
# systemctl enable NetworkManager.service
```
### Setting the root password
```
# passwd
```
### Bootloader
```
# efibootmgr --create --disk DISK --part NUMBER --label "Arch Linux" --loader /vmlinuz-linux --unicode "root=/dev/root_partition resume=/dev/swap_partition rw initrd=\intel-ucode.img initrd=\initramfs-linux.img"
```
### Adding a user
```
# useradd -m -G wheel s3m
```
```
# passwd s3m
```
```
# visudo
```
Uncomment:
```
%wheel ALL=(ALL:ALL) NOPASSWD: ALL
```
### Reboot
```
# exit
```
```
# reboot
```