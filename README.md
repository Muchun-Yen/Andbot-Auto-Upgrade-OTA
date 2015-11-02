# Andbot-Auto-Upgrade-OTA

# Introduction
The document describes the Andbot auto upgrade OTA architecture and updrade mechanisam

# Andbot auto upgrade OTA (A-OTA)
The Andbot auto upgrade OTA (A-OTA) architecture include 3 parts; the Andbot app, colud server, and upgrade system.

#### The A-OTA architecture and work flow diagram.
![A-OTA work flow](https://docs.google.com/drawings/d/1ckOrEvUkxI-c29folQL1CAE7msxt5V7eteUvdp-5i9A/pub?w=552&h=720)
														
# Andbot app
The Andbot app is a background service application program. In A-OTA case, it provides functions as the below
* connecting and syncing up database to cloud server
* check system and upgrade information
* download and check upgrade files
* backup Andbot setting files
* system power monitor
* switch System to upgrade system 

#### Andbot app work flow
--TBD--

# Cloud Server
The servers provide andbot system upgrade files and andbot database in cloud. the architecture as the below
* internet cloud Server	(Amazon EC2)
* system binary image Storage (Amazon S3)
* Andbot database (Amazon DynamoDB)

# Upgrade System
When system buring up from the upgrade u-boot, the root file system will change to busybox, we using bash script to do the upgrade process.
There are some details and related information as the follows

#### Major storage partitions(SD card/eMMC)
![SDcard partitions](https://docs.google.com/drawings/d/1yWVKoBfOmzN5G0ehQm-baXEqNDuhX32Q_PMMvmwtMic/pub?w=629&h=650)

#### Busybox and Linux kernel config

busybox config
```javascript
Busybox Settings --->
	Build Options --->
			[*] Build Busybox as a static binary
```

kernel config
```javascript
File systems  --->  
	DOS/FAT/NT Filesystems  --->
			<*> MSDOS fs support
			<*> VFAT (Windows-95) fs support
			(437) Default codepage for FAT
			(iso8859-1) Default iocharset for FAT
			<M> NTFS file system support
			[ ]   NTFS debugging support
			[*]   NTFS write support
			<*> VFAT (Windows-95) fs support
  
	-*- Native language support  ---> 
			CONFIG_NLS_CODEPAGE_437=y [United States, Canada]
```
 
####busybox fstab and rcS files

etc/init.d/rcS
```javascript
#!/bin/sh
mount -a 
" << upgrating system now... >> "
sync
gzip -dc /warehouse/trusty.img.gz | dd of=/dev/mmcblk0p2 bs=1M
sync
cp /warehouse/BOOT/boot.ini /boot
cp /warehouse/BOOT/exynos5422-odroidxu3.dtb /boot
cp /warehouse/BOOT/uInitrd  /boot
cp /warehouse/BOOT/zImage /boot
sync
reboot
```
etc/fstab

```javascript
UUID=6E35-5356								/boot      vfat  defaults 0 0
UUID=88f52e33-8730-4943-81b0-f22292dd417b	/warehouse ext4  defaults 0 0
```




# References

* busybox 
	* http://www.busybox.net/

* ODROID-XU3 EVM board 
	* http://www.hardkernel.com/main/products/prdt_info.php?g_code=G140448267127
