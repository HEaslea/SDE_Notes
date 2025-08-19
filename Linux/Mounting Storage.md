Detects device when plugged in with USB. 
OS identifies the device and its partitions (separate sections of storage on the device). 

**Creating a Mapping**: OS uses the **block device driver** : interact with the storage device. 

**Block Device** : Any device that transfers data in fixed-size blocks (hard drives or SSDs). 
Then OS communicates through this driver. 

Mount Point : Dir in the OS's filesystem where the contents of the device will appear. 
That's something like `E:` or `F:` (on our pc) for Windows : or `/mnt/usb` on Linux: 

OS checks the filesystem on the storage device (NTFS, ext4, FAT32). 
Filesystem organizes how files are stored and retrieved. 
Uses the filesystem to read and write data from the storage device. 

Remember in C++, when we use `*` - that's the virtual address, not the physical address, with help from the MMU (at least in user space applications). 

## Linux and `/dev`
`/dev` -  the interface to the devices that we put into the system. We can interact with them through these files
`/dev/sda` -> read/write blocks to the disk
`/dev/tty` -> char stream, reading keypress bytes, write chars to screen.

There are four different types of devices: 
Block - holding data in blocks

Char -  streams of just one byte, a char 

Socket -  More research needed //MORERESEARCH

Pipe -  //MORERESEARCH

`/sys` - giving us information and a control path, kernel exposes metadata and config knobs here. 
eg. `/sys/block/sda/size` shows disk size in 512-byte sectors

`/sys/class/net/eth0/address`-> shows MAC address of a NIC. 

`/sys/class/leds/input3::capslock/brightness` where we can write 1 or 0 to toggle keyboard LEDS. This one seems wild, but it's so cool. 

All of this makes a lot of sense and we can see how easy it is to configure what we are doing. 

##### Connecting a USB
Say that we put a storage USB in the system. 
The kernel's USB subsystem detects it and emits a `uevent` a user event I believe. 
The event needs to be worked on, meaning that `udevd` responds by creating a device node (eg. `/dev/sdb`). 

The USB will show up as a block device (stores data in blocks). 

`lsblk` - as in list block devices

There might be something like `sdb`
which might have a partition of `sdb1` in it ( I believe that's another dir in there to show the partitions). 

**The Filesystem on the block device**  : the partition likely has a filesystem similar to `FAT32, exFat, ext4` etc., in order to access that filesystem, 
There's the data, then there's the way to read the data so that it is seen as a filesystem. 
That means we need a way to view the data in order to see how to interpret it into a file system. 
Otherwise we just have blocks of raw data that we don't really read. 
To interpret them, we need a `filesystem`, `FAT32, exFat, NTFS, ext4` and there are more. 
Therefore the filesystem stores metadata that describes certain things : where files are, their names, sizes, permissions, timestamps. 

We mount : 

`sudo mount /dev/sdb1 /mnt/usb`

now `/mnt/usb` will contain the USB files. 

Therefore, when we read and write to files in `/mnt/usb` -> we go through: 
 - Filesystem Driver (ext4, fvat, etc.)
 - Block device layer : `/dev/sdb1`
 - USB Storage Driver
 - Kernel USB subsystem --> actual hardware

When we are done with it, then we can do `sudo umount /mnt/usb`

Most Linux environments run helpers that auto-mount removable drivers under 
`/media/<username>/...`

