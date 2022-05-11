# nanopi-m4-armbian-openzfs WIP!
Instructions to help you setup a ZFS root file system with RAIDZ1 on a nanopi-m4 with 4 port SATA hat
## Requirements
- nanopi m4
- 4xSATA hata
- Armbian installation with bullseye 5.15.x kernel
## Aim
This installation stores the SPL, u-boot and initramfs files on the SD card and while the root filesystem is stored on the raid drive. U-boot and initramfs will be configured to bypass the root file system on the SD card and to load from the zpool dataset root file system.

## Quick Overview of Installation Steps
- Build Armbian from source - since current versions of Armbian didn't seem to support SD card boot for my NanoPi M4.
- Install necessary ZFS packages
- Setup RAIDZ drives rpool a MIRROR could also be used
- Modify initramfs modules to load and rebuild initramfs
- Update initramfs
- Modify armbianENV.txt to use bootargs (extraargs) to boot from ZFS ROOT with  
- Copy root file system from SD card to zpool dataset using rsync
- Reboot

## The details
Before starting it is useful to describe the boot process of this NanoPi M4. After a hardware reset the boot ROM loads a SPL boot loader (secondary program loader - a stripped down version of u-boot) from the SD card, memory is configured and then u-boot is started in full. U-boot then loads the kernel and (in this case) the initial RAM disk initramfs, and then transfers control the kernel.

Download build Armbian using the [Armbian Build Documentation](https://docs.armbian.com/Developer-Guide_Build-Preparation/).

```
apt install -t bullseye-backports zfs-dkms zfsutils-linux zfs-initramfs 
``` 

## Post Install
remember SD card needs to be mounted
any changes to initramfs need to be copied to the SD card
/boot that is used by u-boot is not on the raid drive

## Troubleshooting
Stuck in initramfs
Manual booting zfs pool
```
zpool import rpool
Mount -t zfs rpool/BOOT\debian-1 /root
exit
```
Manual booting from the SD card
```
mount /dev/mmcblk1p1 /root #sometimes /dev/mmcblk0p1
exit
```

## Future work
Get u-boot to look for initramfs from raid drive instead of SD card
