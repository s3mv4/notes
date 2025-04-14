# Arch Linux Installation Guide
## Before booting
### Getting an ISO file
Get an ISO file from [archlinux.org](https://archlinux.org/download) (scroll down and choose a mirror according to your country) (cj2.nl for the Netherlands).
### Flashing a USB drive
Flash a USB drive with the ISO file.

If you are on Windows I would choose Rufus to flash the USB drive.

Balena Etcher can also do the job although I haven't used it much. It supports Linux, MacOS and Windows.
### Booting the live environment
Insert the USB drive into the laptop on which you want to install Arch Linux.

Make sure secure boot and fast boot are off, do this in the BIOS settings.

To boot from USB drive you need to choose it as a temporary startup device.

On the Lenovo Thinkpad X1 Carbon you can spam enter when starting the device and select what to do, press F12 for the temporary startup device option and choose *USB HDD* from there.
## Inside the live environment
You should now find yourself inside the live environment.
### Keyboard layout
You can load an alternative keyboard layout like so:
```
# loadkeys dvorak-programmer
```
### Internet
Now connect to the internet using iwctl:
```
# iwctl
```
```
[iwd]# station wlan0 connect SSID
```
With `SSID` being your Wi-Fi's SSID.

Now enter the password, press Ctrl+d to exit the iwd prompt and you should be connected to the internet.

You can check this using:
```
# ping archlinux.org
```
If you are getting bytes of data then you are connected. Ctrl+c to end the ping command.
### Partitioning the disks
We can partition the disks using fdisk.

First to list all disks available:
```
# fdisk -l
```
And then decide on which disk you want to use.

Be careful on what disk you choose as you could end up wiping stuff accidentally.

Usually computers have one disk which is the largest, the USB drive will also show up.

Once you have decided what disk to use then you can partition that disk using:
```
# fdisk DISK
```
With `DISK` of course being the chosen disk.

Example:
```
# fdisk -l
Disk /dev/nvme0n1: 238.47 GiB, 256060514304 bytes, 500118192 sectors
Disk model: SAMSUNG MZVLW256HEHP-000L7
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 83895FDA-6CEA-4C65-A72A-4B024C642CED
 
Device            Start       End   Sectors   Size Type
/dev/nvme0n1p1     2048   2099199   2097152     1G EFI System
/dev/nvme0n1p2  2099200  10487807   8388608     4G Linux swap
/dev/nvme0n1p3 10487808 500117503 489629696 233.5G Linux filesystem
 
 
Disk /dev/sda: 29.06 GiB, 31198642176 bytes, 60934848 sectors
Disk model: Innostor
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x0022a1c8
```
In this example there are 2 disks, the bottom one being the USB drive judging from the disk model and size. 

So we want to partition the disk at the top:
```
# fdisk /dev/nvme0n1
```
You can press m for help when you are inside the fdisk prompt, this shows various actions you can perform on the disk.

We first need to create a partition table and most modern computers use GPT, so we enter g:
```
Command (m for help): g
```
Then enter n for a new partition:
```
Command (m for help): n
```
It will ask what the partition number should be and since this is our first partition on this table we will enter 1:
```
Partition number (1-128, default 1): 1
```
First sector just press enter

Last sector is where the partition should end and that equals to the size of the partition.

The first partition we create is going to be the EFI system partition, Arch Wiki recommends it to be 1G but that is overkill since we are not going to dual boot or have multiple kernels, so 128 megabytes should be plenty:
```
Last sector. +/-sectors or +/-size{K,M,G,T,P} (2048-500118158, default 500117503): +128M
```
It could ask to remove a signature, just enter 'y' or 'yes' and press enter.

We need to change the type of the EFI system partition accordingly:
```
Command (m for help): t
```
```
Partition type or alias (type L to list all): 1
```
And that is one partition done

Now we create a swap partition, used to hibernate and for extra RAM, we are not going to hibernate so 4 gigabytes should be fine:
```
Command (m for help): n
```
```
Partition number (1-128, default 2): 2
```
First sector, again press enter.
```
Last sector. +/-sectors or +/-size{K,M,G,T,P} (264192-500118158, default 500117503): +4G
```
```
Command (m for help): t
```
```
Partition number (1,2, default 2): 2
```
```
Partition type or alias (type L to list all): 19
```
Ok, now onto the final partition, this is actually where all your other files will go and it should be the remaining space available on the disk:
```
Command (m for help): n
```
```
Partition number (1-128, default 3): 3
```
First sector, again press enter.

Last sector, *also* press enter.

Last partition type is fine by default, so now to save all changes, write the table to the disk and exit:
```
Command (m for help): w
```
### Formatting the partitions
Remember we made 3 different partitions? To list them use:
```
# fdisk -l
```
You can tell the partitions apart by their size and type:
```
Device            Start       End   Sectors   Size Type
/dev/nvme0n1p1     2048    264191    262144   128M EFI System
/dev/nvme0n1p2   264192   8652799   8388608     4G Linux swap
/dev/nvme0n1p3  8652800 500117503 491464704 234.3G Linux filesystem
```
In this example:

The EFI system partition is the one with EFI System as its type, so /dev/nvme0n1p1.

The swap partition is the one with Linux swap as its type, so /dev/nvme0n1p2.

The root partition is the one with Linux filesystem as its type, so /dev/nvme0n1p3.
___
Refer to this table in future commands:
| Name                   | Device      |
|------------------------|-------------|
| `efi_system_partition` | `nvme0n1p1` |
| `swap_partition`       | `nvme0n1p2` |
| `root_partition`       | `nvme0n1p3` |
___
There are a lot of different file systems but ext4 is the simplest one and therefore we will use it.

To create an ext4 file system on the root partition:
```
# mkfs.ext4 /dev/root_partition
```
We must initialize the swap partition so it can be used:
```
# mkswap /dev/swap_partition
```
And finally we need to format the EFI system partition:
```
# mkfs.fat -F 32 /dev/efi_system_partition
```
### Mounting the filesystems
We now need to mount the filesystems in order to modify them from the live environment:
```
# mount /dev/root_partition /mnt
```
```
# mount --mkdir /dev/efi_system_partition /mnt/boot
```
And to enable the swap partition:
```
# swapon /dev/swap_partition
```
### Installing essential packages
Our system needs a few essential packages in order to ensure it works properly, you can install any package here but I like to keep it minimal for now and install other packages later on.

We need a CPU microcode, which depending on your hardware should be `amd-ucode` or `intel-ucode` (I'm on a thinkpad so intel for me).

A boot loader, in my case `efibootmgr`.

A network manager, in my case `networkmanager`.

A text editor, `vim`.

Documentation package, `man`.

`sudo` to manage root access.

`git` for version control of coding projects.

To install these packages we need to pacstrap them to our system via the live environment:
```
# pacstrap -K /mnt base base-devel linux linux-firmware intel-ucode efibootmgr networkmanager vim man sudo git
```
### Fstab
We need to generate an fstab file which will basically store how we have our partitions mounted in the live environment and the main system will use that file after the installation.

To generate it:
```
# genfstab -U /mnt >> /mnt/etc/fstab
```
### Chroot
Now we can chroot into the new system and configure it from within.

To do this:
```
# arch-chroot /mnt
```
### Time
We need to set our time zone and synchronize the hardware clock with the system clock.

To set the time zone:
```
# ln -sf /usr/share/zoneinfo/REGION/CITY /etc/localtime
```
With `REGION` being your region and `CITY` being your city.

In my case: `Europe/Amsterdam`.

And to sync the hardware clock and system clock:
```
# hwclock --systohc
```
It is also smart to enable NTP to ensure that the system clock stays synchronized with the time servers.

We can do this by running:
```
# timedatectl set-ntp true
```
### Localization
We need to edit and generate locales for correct formatting of things like dates, times, currency and numbers for our region.

To do this we need to edit /etc/locale.gen:
```
# vim /etc/locale.gen
```
And uncomment `en_US.UTF-8 UTF-8` (scroll with j/k and uncomment by removing hashtag using x and quit with :wq).

Now generate the locales with:
```
# locale-gen
```
Now create the locale.conf file:
```
# vim /etc/locale.conf
```
And have it contain the following (i to type, esc to exit, :wq to quit):
```
LANG=en_US.UTF-8
```
Now if you changed the keyboard layout you need to edit /etc/vconsole.conf accordingly:
```
# vim /etc/vconsole.conf
```
And have it contain the following:
```
KEYMAP=dvorak-programmer
```
### Network configuration
The hostname is basically the name of your device and it doesnt really matter unless you need to identify it via network which I never need to do.

The most basic hostname is 'archlinux' so we will use that:
```
# vim /etc/hostname
```
And have it contain your hostname, so in our case:
```
archlinux
```

Also enable the NetworkManager service:
```
# systemctl enable NetworkManager.service
```
### Setting the root password
You can set the root (admin user) password with the following command:
```
# passwd
```
### Boot loader
A boot loader will start the kernel when you power on your computer, without it you cant use your computer.

Efibootmgr or EFI stub is by far the fastest boot loader if you can even call it that, it is basically a command which writes directly to the UEFI firmware settings and doesn't need to be loaded into memory before starting.

You only have to run the command once and it will remember it and boot automatically from that point onward.

This is the command and I will break it down below:
```
# efibootmgr --create --disk DISK --part NUMBER --label "Arch Linux" --loader /vmlinuz-linux --unicode "root=/dev/root_partition resume=/dev/swap_partition rw initrd=\intel-ucode.img initrd=\initramfs-linux.img"
```
With `DISK` being the disk we are using (fdisk -l), in our case that would be /dev/nvme0n1.

With `NUMBER` being the partition number on which the EFI system partition is located, in our case 1.

For `root_partition` and `swap_partition` refer to the table.

The `intel-ucode` can be replaced by `amd-ucode` if necessary.
### Adding a user
It is smart to add a user with sudo to our system rather than using root, because this can prevent any damaging actions to take place and save our system.

To create a new user and add it to the wheel group (which allows access to sudo) we run this command:
```
# useradd -m -G wheel USER
```
And to set the password of the user:
```
# passwd USER
```
With `USER` being the username of the added user.

Now change the sudoers file to allow the wheel group to be able to use sudo:
```
# visudo
```
And uncomment `%wheel ALL=(ALL:ALL) NOPASSWD: ALL`.
### Reboot
All done!

Exit the chroot environment with Ctrl+d

And run:
```
# reboot
```
## Post install
After the reboot you can connect to the internet using `nmtui`, it will open a terminal user interface which is very intuitive.