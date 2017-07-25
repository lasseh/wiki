# Installing Archlinux /w Full Disk Encryption on Macbook Pro ~2015

## Preparations
* Install rEFIt via OS X http://refit.sourceforge.net/
### Creating a LiveUSB
#### MacOS
```bash 
diskutil list
diskutil unmountDisk /dev/sda
sudo dd if=/Users/you/archlinux-foo.iso
```

## Before installing
* Hold the option key while booting, boot from the USB device

### Keyboard
```bash
loadkeys no
```
### Time and date
timedatectl set-ntp true

### Internet
#### Wired
Just start dhcpcd
```bash
systemctl start dhcpcd 
```

#### Wireless
```bash
systemctl start netctl
wifi-menu # Configure your connection
ping -c 1 8.8.8.8
```

## Preparing your install medium
### Preface
The target install medium is a 64GB USB3 device. The entire system is encrypted,
except the last partition which is an EFI boot partition. rEFI is installed on the system 
via OS X earlier.

```Layout
+------------------------------------------+
|   Target install medium                  |
|   USB at /dev/sda                        |
|----------------------------------+-------|
|   LUKS container encapsulating   |       |
|   LVM Partitions                 | EFI   |
+-------------+--------------------+ 64M   |
|             |                    |       |
|  LV: /      | LV: /home          |       |
| 16G         | 48G                |       |
|             |                    |       |
|             |                    |       |
+-------------+----------------------------+
```

### 1. Overwrite medium with random data
Overwrite the entire target install medium with random data. This
removes any previous files and makes cryptoanalysis harder. Obviously
this makes any data on the target medium permanently unrecoverable.

/dev/sda is the USB disk I'm installing to. Keep in mind that this might not 
be the same every time you boot.

This might take a few hours, depening on the size and speed of the medium.
```bash
dd if=/dev/urandom of=/dev/sda bs=4096 status=progress && sync
``` 

### Partitioning
We are using GUID Partition Table (GPT) since we are using UEFI boot. UEFI requires a
EFI System Partition (ESP / EFI System) between 100 and 512 MiB. This partition wont (cant)
be encrypted. We will also need a traditional boot-partition for linux, but this we will 
be able to encrypt. The rest of the disk will be used as a LUKS container, containging LVM.

#### Create the GPT
```
1 /dev/sdc1	256 MiB	EF00	EFI System		BOOTABLE
2 /dev/sdc3 	256 MiB	8300	Linux Filesystem
3 /dev/sdc3	57  MiB 8E00	Linux LVM
```

### Encrypted LUKS Container
* XTS Mode splits key size in half so to use AES256 instead of AES128 we double the key size to 512.
* iter-time is upped slightly from the default at 2000, this makes brute forcing even more imporactical.
* Even though SHA256 is by far secure enough, SHA512 is less practical to crack with GPU's and ASIC hardware
  probably doesnt exist.

#### Cryptsetup
```bash
cryptsetup -v --cipher aes-xts-plain64 --key-size 512 \
	--hash sha512 --iterm-time 3000 --use-urandom \
	--verify-passphrase luksFormat /dev/sdc3
Enter passphrase: *Secret Sauce*
Verify passphrase: *secret Sauce*
```

#### The LUKS Header
* The header is visible without opening the luks container
* Losing or damaging the header the header makes your data junk. Consider making a backup
```bash
cryptsetup luksDump /dev/sdc3
cryptsetup luksHeaderBackup /dev/sdc3 --header-backup-file you-luks-header.bak
```

Before continuing, open the LUKS container and give it a suitable name. Mine is CryptLinux and the
devices will be mapped to and available under /dev/mapper 
```bash
cryptsetup luksOpen /dev/sdc3
```

### LVM Setup
#### Create the PV and VG
Create the Physical Volume on top of the LUKS container
```bash
pvcreate /dev/mapper/Crypt
```

Create the Volume Group (Archlinux in my case) on the Physical Volume
```bash
vgcreate Archlinux /dev/mapper/Crypt
```

#### Create the Logical Volumes
Create the Logical volume inside the Archlinux. I will use a root
volume and a home volume and I will leave some room to spare in case of 
cool ideas in hindsight.. I will NOT be creating a swap partition because 
the system is running on a flash drive with limited writes. This part 
varies from system to system, do whatever you want. (Use "-l 100%FREE" to use the remaining
space)
```bash
lvcreate -L 16G Archlinux -n root
lvcreate -L 32G Archlinux -n home
```

### Formatting the filesystems
#### Format the EFI System
The EFI System needs to be FAT32
```bash
mkfs.fat -F32 /dev/sdc1
```

#### Format the boot filesystem
The boot filesystem will be left unencrypted for now.
```bash
mke2fs -t ext2 /dev/sdc2

#### Formatting root and home
We'll use normal EXT4 on the partitions inside the LUKS/LVM container. These will be mounted with
noatime to reduce write load/wear on the flash drive in this case, but that comes later.
```bash
mke2fs -t ext4 /dev/Archlinux/root
mke2fs -t ext4 /dev/Archlinux/home
```

### Mounting the filesystems
```bash
mount -o noatime /dev/Archlinux/root /mnt
mkdir /mnt/home
mount -t ext4 -o noatime /dev/Archlinux/home /mnt/home
mkdir /mnt/boot
mount /dev/sdc1 /mnt/boot
```

### Install Archlinux
Install the base system
```bash
pacstrap /mnt base
``` 

Generte the fstab
```bash
genfstab -U /mnt >> /mnt/etc/fstab
``` 

Enter the chroot
```bash
arch-chroot /mnt
```

### Inside Chroot
# ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

# hwclock --systohc

uncomment en_US.UTF-8
# locale-gen

mkinitcpio.conf
HOOKS=" ... keyboard block encrypt lvm2 ... filesystems ..."
mkinitcpio -p linux

GRUB_CMDLINE_LINUX="... cryptdevice=<UUID-OF-LVM-DEVICE>:dmname ..."
GRUB_ENABLE_CRYPTODISK=y

grub-mkconfig -o /boot/grub/grub.cfg
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub --removable --recheck



