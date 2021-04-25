# Linux Basics and System Startup

## Learning Objectives

* Identify Linux filesystems
* Identify the differences between partitions and filesystems
* Describe the boot process
* Install Linux on a computer


## The Boot Process

### The Boot Process

The Linux boot is the procedure for initializing the system.
It consists of everything from when the computer power is first switched until the user interface is fully operational.

### BIOS - The First Step

When the computer is powered on, the *Basic Input Output System (BIOS)* initializes the hardware, including the screen and keyboard, and tests the main memory.
This process is called *POST (Power On Self Test)*.

The BIOS software is stored on a ROM chip on the motherboard.
After this step, the rest of the boot process is controlled by the operating system.

### Master Boot Record (MBR) and Boot Loader

Once the POST is completed, the system control passed from the BIOS to the *boot loader*.
The boot loader is usually stored on one of the hard disks in the system, either in the boot sector (for more traditional BIOS/MBR systems) or the *EFI* partition (for more recent *Unified Extensible Firmware Interface* or *EFI/UEFI* systems).
Up to this stage, the machine does not access any mass storage media.
Thereafter, info on date, time, and important peripherals are loaded from the CMOS values.
CMOS is a battery-powered memory store that allows the system to keep track of date and time even when powered off.

The most common boot loaders for Linux are *GRUB (Grand Unified Bootloader)*, *ISOLINUX* for booting from removable media, and *DAS U-Boot* for booting on embedded devices/appliances.
Most Linux bootloaders can present a UI for choosing boot options.

When booting Linux, the bootloader is responsible for loading the kernel image and the initial RAM disk or filesystem (containing critical files and device drivers needed to start the system) into memory.
