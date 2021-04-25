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

The most common boot loaders for Linux are *GRUB (Grand Unified Boot loader)*, *ISOLINUX* for booting from removable media, and *DAS U-Boot* for booting on embedded devices/appliances.
Most Linux boot loaders can present a UI for choosing boot options.

When booting Linux, the boot loader is responsible for loading the kernel image and the initial RAM disk or filesystem (containing critical files and device drivers needed to start the system) into memory.

### Boot Loader in Action

The boot loader has two distinct stages:

For systems using the BIOS/MBR method, the boot loader resides at the first sector of the hard disk, known as the Master Boot Record.
The size of the MBR is 512 bytes.
In this stage, the boot loader examines the *partition table* and finds a bootable partition.
It then searches the bootable partition for the second-stage boot loader (like GRUB) and loads it into RAM.
For systems using the EFI/UEFI method, UEFI firmware reads its Boot Manager data to determine which UEFI application is to be launched and at which disk & partition it is located. The firmware then launches the UEFI application (like GRUB) as identifed from the boot manager.
This procedure is more complicated but more versatile than older MBR methods.

The second stage boot loader resides under `/boot`.
A splash screen is displayed to allow the user to choose which OS to boot.
The boot loader then loads the kernel of the selected OS into RAM and passes control to the kernel.
Kernels are almost always compressed, so the kernel's first job is to uncompress itself.
After this, it will check and analyze the system hardware and initialize and device drivers built into the kernel.

### Initial RAM Disk

The *initramfs* filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem, like providing kernel functionality for the filesystem and device drivers for mass storage controllers with a facility called *udev (user device)*.
Udev is responsible for figuring out which devices are present, locating the device drivers they need to operate properly, and loading the drivers.
After the root filesystem has been found, it is checked for errors and mounted.

The *mount* program instructs the operating system that a filesystem is ready for use, and associates it with a particular point in the overall hierarchy of the filesystem (the *mount point*).
If this is successful, the initramfs is cleared from RAM and the init program on the root filesystem (`/sbin/init`) is executed.

*Init* handles the mounting and pivoting over to the final real root filesystem.
If special hardware drivers are needed before the mass storage can be accessed, they must be in the initramfs image.

### Text-Mode Login

Near the end of the boot process, *init* starts a number of text-mode login prompts. These enable you to type your username, followed by your password, and to eventually get a command shell.
If you are running a system with a graphical login interface, you will not see the text-mode login prompts.

The terminals which run the command shells can be accessed by using the `alt` key plus a `function` key.
Most distros start six text terminals and one graphics terminal starting with `f1` or `f2`.
Within a graphical environment, switching to a text console requires pressing `ctrl + alt +` the appropriate `function` key, with `f1` or `f7` leading to the GUI.

Usually the default command shell is *bash (GNU Bourne Again Shell)*, but there are a number of other advanced command shells available.
