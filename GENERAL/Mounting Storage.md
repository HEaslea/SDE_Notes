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



