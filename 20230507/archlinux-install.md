# UEFI entries
If you can boot into Linux in UEFI mode, use efibootmgr:

- ```$ efibootmgr``` or ```$ efibootmgr -v``` to list entries
- ```$ efibootmgr -b XXXX -B``` to delete an entry by number

# wifi connection
```
$ iwctl
station wlan0 get-networks
station wlan0 connect ENDPOINT_NAME
```

# partition
### check table
```
$ lsblk
$ fdisk -l
```

### partitioning tools
```$ cgdisk``` or ```$ cfdisk```

See https://wiki.archlinux.org/title/partitioning#Tools

### formatting
```
$ mkfs.fat -F 32 /dev/efi_partition
$ mkfs.ext4 /dev/root_partition
$ mkfs.ext4 /dev/home_partition
$ mkswap /dev/swap_partition
```

### mount file systems
```
$ mount /dev/partitionID /mnt    #root
$ mount /dev/partitionID /mnt/home    #home(first mkdir /mnt/home)
$ mount /dev/partitionID /mnt/boot/efi   #efi(for dual boot, first mkdir /mnt/boot/efi)
$ swapon /dev/swap_partition
```

See https://wiki.archlinux.org/title/installation_guide#Partition_the_disks

# Install base system
```
$ pacstrap -i /mnt linux linux-headers linux-firmware base base-devel vi intel-ucode
```

# Configure
### Generate fstab
```
$ genfstab -U -p /mnt >> /mnt/etc/fstab
```

### Chroot(get into the installed system)
```
$ arch-chroot /mnt
```

### Set timezone
```
$ ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
$ hwclock --systohc
```

### locale
Edit ```/etc/locale.gen``` and uncomment ```en_US.UTF-8 UTF-8```

```
$ locale-gen
$ echo LANG=en_US.UTF-8 > /etc/locale.conf
```

### network setting
```
$ echo myhostname > /etc/hostname
```

Edit /etc/hosts as following

```
127.0.0.1	localhost
::1		localhost
127.0.0.1	myhostname.localdomain	localhost
```

### set root password
```
$ passwd
```

### add user
```
$ useradd -m -g users -G wheel,storage,power username
$ passwd username
```

```$ visudo``` then uncomment ```%wheel ALL=(ALL:ALL) ALL``` for super user


# Install bootloader
### sample as grub
See https://wiki.archlinux.org/title/Arch_boot_process#Boot_loader

See https://wiki.archlinux.org/title/GRUB

```
$ pacman -S grub efibootmgr os-prober
$ grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi
```

Edit ```/etc/default/grub```, uncomment ```GRUB_DISABLE_OS_PROBER=false```

Generate grub config file

```
$ grub-mkconfig -o /boot/grub/grub.cfg
```

# Install network and other necessary things
```
$ pacman -S networkmanager network-manager-applet dialog wpa_supplicant dhcpcd
$ systemctl enable NetworkManager
$ pacman -S mtools dosfstools bluez bluez-utils xdg-utils xdg-user-dirs alsa-utils pulseaudio pulseaudio-bluetooth reflector openssh
```

# Graphic card driver
See https://wiki.archlinux.org/title/NVIDIA

```
$ pacman -S nvidia nvidia-utils lib32-nvidia-utils
```

Remove kms from the HOOKS array in /etc/mkinitcpio.conf and regenerate the initramfs

Add nvidia, nvidia_modeset, nvidia_uvm and nvidia_drm to MODULES

```
$ mkinitcpio -P
```

Make hook

```
$ vim /etc/pacman.d/hooks/nvidia.hook
```

```
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update NVIDIA module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```

To enable DRM (Direct Rendering Manager) kernel mode setting, add the nvidia_drm.modeset=1 kernel parameter.

```
$ vim /etc/default/grub
```

Add to ```GRUB_CMDLINE_LINUX_DEFAULT```, re-generate grub config file

```
$ grub-mkconfig -o /boot/grub/grub.cfg
```

# Install desktop environment
```
$ pacman -S kde sddm konsole neofetch dolphin git wget kate ark p7zip
$ systemctl enable sddm
```

# Reboot
```
$ exit
$ umount -R /mnt
$ reboot now
```

# Another trial
Just use ```archinstall``` using MATE this time

Just exactly what I want, much better than LXQt

NOTE: To mount NTFS partition, MATE needs ```ntfs-3g``` package installed
