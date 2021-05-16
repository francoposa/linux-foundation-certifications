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

## Kernel, init, and Services

### The Linux Kernel

The boot loader loads both the kernel and the initial RAM-based filesystem (initramfs) into memory so it can be used directly by the kernel.

When the kernel is loaded in RAM, it initializes and configures the computer's memory and all the hardware attached to the system - all processors, I/O subsystems, storage devices, etc.
The kernel also laods some necessary userspace applications.

### /sbin/init/ and Services

Once the kernel has set up all its hardware and mounted the root filesystem, it runs `/sbin/init`.
This becomes the initial process which starts other processes to get the system running.
Most other processes on the system trace their origin ultimately to *init*.
Exceptions include the "kernel processes", started by the kernel directly to manage internal operating system details.

Besides starting the system, *init* is responsible for keeping the system running and shutting it down cleanly.
One of its responsibilities is to act when necessary as a manager for all non-kernel processes; it cleans up after them on completion, restarts user login services on user login/logout, and does the same for other background system services.

Traditionally this process startup was done using conventions dating back to the 1980s System V variety of UNIX.
The sysvinit method was a serial process, with the system passing through a series of *runlevels* containing collections of scripts that start and stop services.
Each runlevel supports a different mode of running the system.
In each runlevel, services can be set to start, or shut down if already running.

More recently, all major distros have moved away from this sequential approach, though they often continue to support sysvinit for compatibility purposes.

### Startup Alternatives

*Sysvinit*'s  serial process viewed everything as sequential stages, each requiring completion before another could start.
Startup therefore did not take advantage of parallel processing available on multicore machines.

Further, sysvinit viewed shutdown and reboot as rare events, so it did not matter how long these processes took.
As Linux systems evolved to require faster and in some cases near-instant startup times, they required startup methods with faster and more advanced capabilities
Finally, older methods used complex startup scripts that were difficult to keep in sync across distros, kernel versions, architectures, etc.

Two major alternatives were developed: Upstart and systemd.

Systemd has won out in the vast majority of distros, though not with some controversy.
Systemd provides many features, which makes it an effective one-stop-shop for system startup and service management, though many feel that it violates the UNIX philosophy of each tool only having a single purpose.

### Systemd Features

In distros using systemd, `/sbin/init` now just points to `/lin/systemd/systemd`.

Systemd starts up faster than earlier init methods due to its aggressive parallelization techniques.

Complex startup shell scripts are replaced with simpler config files.
The configs enumerate what has to be done before a service starts, under which conditions it should be started, and what conditions indicate the service has completed its startup.

Systemd also provides command-line utilities including `systemctl` for service management.

## Linux Filesystem Basics

### Partitions and Filesystems

A **partition** is a physically contiguous section of a desk, or what appears to be physically contiguous in some advanced setups.

A **filesystem** is a method of storing and finding files on a hard disk, usually within a single partition.

Generally a partition is a container in which a filesystem resides, though filesystems can span more than one partition with the use of symbolic links.


### The Filesystem Hierarchy Standard

Linux systems store their important files according to a standard layout, the **Filesystem Hierarchy Standard (FHS)**, which is maintained by the Linux Foundation.

All Linux filesystem names are case-sensitive.

Many distros distinguish between core utilities needed for proper system operation, and other programs, placing the latter in the `/usr` directory.

## Linux Distribution Installation

### Questions to Ask When Choosing a Distribution

* What is the main function of the system (server or desktop)?
* What types of packages are important to the organization?
* How much hard disk or RAM space is required and how much is available?
* How often are packages updated?
* How long is the support cycle for each release?
* Do you need kernel customization from the vendor or a third party?
* What hardware are you running on?
* Do you need long-term stability? Can you accept (or need) a more volatile cutting edge system running the latest software?
