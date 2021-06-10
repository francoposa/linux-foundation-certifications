# File Operations

## Learning Objectives

* Explore the filesystem and its hierarchy
* Explain the filesystem architecture
* Compare files and identify different file types
* Back up and compress data

## Filesystems

### Introduction to Filesystems

In Linux (and all UNIX-like operating systems) it is often said "Everything is a file", or at least it is treated as such.
This means that whether you are dealing with normal data files and documents, or with devices such as sound cards and printers, you interact with them through the same kind of I/O operations.
This simplifies things: to do pretty much anything, you open a "file" and perform normal operations like reading the file and writing to it.

On many systems, including Linux, the filesystem is structured like a tree.
The tree is usually portrayed as inverted, and starts at what is most often called the **root directory**, which marks the beginning of the hierarchical filesystem and is also sometimes referred to as the trunk, or simply denoted by `/`.
The root directory is not the same as the root user.
The hierarchical filesystem also contains other elements in the path (directory names), which are separated by forward slashes (`/`) as in `/usr/bin/vim`, where the last element is the actual file name.

### Filesystem Varieties

Linux supports a number of native filesystem types, expressly created by Linnux developers, such as ext3, ext4, squashfs, btrfs, and more.

It also offers implementations of filesystems used on other operating systems, such as those from Windows (ntfs, vfat), SGI (xfs), IBM (jfs), and MacOS(hfs, hfs+).

Many older, legacy filesystems, such as FAT, are also supported.

It is often the case that more than one filesystem type is used on a machine, based on considerations such as the size of files, how often they are modified, what kind of hardware they sit on, what kind of access speed is needed, etc.
The most advanced filesystem types in common use are the **journaling** varieties: ext4, xfs, btrfs, and jfs.
These have many state-of-the-art features and high performance, and are very hard to corrupt accidentally.

### Linux Partitions

Each filesystem on a Linux system occupies a disk **partition**.
Partitions help to organize the contents of disks according to the kind and use of the data contained.
Foe example, important programs required to run the system are often kept on a separate partition (known as `root` or `/`) than the one that contains files owned by regular users of that system (`/home`).
In addition, temporary files crated and destroyed during normal operations may be located on dedicated partitions.
One advantage of this kind of isolation by type and variability is that when all available space on a particular partition is exhausted, the system may still operate normally.

### Mount Points

Before you can start using a filesystem, you need to **mount** it on the filesystem tree at a mount point.
A mount point is simply a directory, which may or may not be empty, where the filesystem is to be grafted on.
You may need to create a directory if it does not already exist.

**Warning**: If you mount a filesystem on a non-empty directory, the former contents of that directory are covered up and not accessible until the filesystem is unmounted.
Thus, mount points are usually empty directories.

### Mountnig and Unmounting

The `mount` command is sued to attach a filesystem (which can be local to the computer or on a network) somewhere within the filesystem tree.
The basic arguments are the **device node** and mount point.
For example:

```
$ sudo mount /dev/sda5 /home
```

will attach the filesystem contained in the disk partition associated with the `/dev/sda5` device node into the filesystem tree at the `/home` mount point.
There are other ways to specify the partition other than the device node, such as using the disk label or UUID.

To unmount the partition, the command would be:

```
$ sudo umount /home
```

Note that the command is `umount` not unmount.
Root privileges are required to run these commands unless the system has been otherwise configured.

To make a fileystem automatically available every time the system starts up, you need to edit `/etc/fstab` (short for filesystem table) accordingly.
You can view the fstab file to see the configuration of all pre-configured filesystems, and use `man fstab` to see how the file is used and configured.

Executing `mount` without any arguments will show all presently mounted filesystems.

The command `df -Th` (disk-free) will display information about mounted filesystems, including the filesystem type and usage statistics about currently used and available space.

### NFS and Network Filesystems

A network filesystem, sometimes called a distributed filesystem, may ehave all of its data on one machine or have it spread out on more than one network node.
A variety of different filesystems can be used locally on the individual machines; a network filesystem can be though of as a grouping of lower lever filesystems of varying types.

Many system administrators mount remote users' home directories on a server in order to give them access to the same files and configuration files across multiple client systems.

The most common such filesystem is named **NFS** for Network File System, which has a very long history and was first developed by Sun Microsystems.

### NFS on the Server

On the server machine, NFS uses **daemons** (built-in networking ans service processes in Linux).
These and other system servers are started at the command line:

```
$ sudo systemctl start nfs
```

The text file `/etc/exports` contains the directories and permissions that host is willing to share with other systems over NFS.
A simple entry in this file may look like the following:

```
/projects *.example.com(rw)
```

This entry allows the directory `/projects` to be mounted using  NFS with read and write permissions and shared with other hosts in the example.com domain.
As we will detail in the next chapter, every file in Linux has three possible permissions: read (`r`), write (`w`), and execute (`x`).

After modifying the `/etc/exports` file, you type `exportfs -av` to notify Linux about the directories you are allowing to be remotely mounted using NFS.
You can also restart NFS with `sudo systemctl restart nfs`, but this is heaver, as it halts NFS for a short while before starting it again.

To make sure the NFS service starts whenever the system is booted, issue `sudo systemctl enable nfs`.

### NFS on the Client

One the client machine, if it is desired to have the remote filesystem mounted automatically upon system boot, `/etc/fstab` si modified to accomplish this.
An entry in the in the client's fstab may look like the following:

```
server:/projects /mnt/nfs/projects nfs defaults 0 0
```

You can also mount the remote filesystem without a reboot or as a one-time mount by directly using the `mount` command:

```
$ sudo mount servername:/projects /mnt/nfs/projects
```

Remember that if `/etc/fstab` is not modified, this remote mount will not be present after system restart.
Further, you may want to add the `nofail` option in the fstab to allow the client system to boot when the NFS server is not available.

## Filesystem Architecture

### The /bin and /sbin Directories

The `/bin` directory contains executable binaries, essential commands used to boot the system or operate in single-user mode, and essential commands required by all system users, such as cat, cp, ls, mv, ps, and rm.

The `/sbin` directory is intended for essential binaries related to system administration, such as fsck and ip.

To view these programs:

```
$ ls /bin /sbin
```

Commands that are not essential (theoretically) for the system to boot or operate in single-user mode are placed in the `/usr/bin` and `/usr/sbin` directories.

Historically, this was done so `/usr` could be mounted as a separate filesystem at a later stage of system startup or even over a network.
Today, this distinction is mostly obsolete.
In fact, many distros have been discovered to be unable to boot with this separation, as this modality had not been used or tested for a long time.

Thus, on some of the newest Linux distros, `/usr/bin` and `/bin` are actually just symbolically linked together, as are `/usr/sbin` and `/sbin`.

### The /proc Filesystem

Certain filesystems, like the one mounted at `/proc`, are called **pseudo-filesystems** because they have no permanent presence anywhere on the disk.

The `/proc` filesystem contains virtual files (files that exist only in memory) that permit vieweing constantly changing kernel data.
`/proc` contains files and directories that mimic kernel structures and configuration information.
It does not contain real files, just runtime system information, e.g. system memory, devices mounted, hardware configuration, etc.
Some important entries in `/proc` are:

`/proc/cpuinfo`
`/proc/interrupts`
`/proc/meminfo`
`/proc/mounts`
`/proc/partitions`
`/proc/version`

`/proc` has subdirectories as well, including:

`/proc/<Process-ID-#>`
`/proc/sys`

### The /dev Directory

The `/dev` directory contains **device nodes** a type of pseudo-file used by most hardware and software devices, except for network devices.
This directory is:

* Empty on the disk partition when it is not mounted
* Contains entires which are created by the **udev** system, which creates and manages device nodes on Linux, creating them dynamically when devices are found.

The `/dev` directory contains items such associated

1. `/dev/sda1`: first partition on the first hard disk
2. `/dev/lp1`: second printer
3. `/dev/random`: a source of random numbers

### The /var Directory

The `/var` (variable) directory contains files that are expected to change in size and content as the system is running, such as:

* System log files: `/var/lib`
* Packages and database files: `/var/lib`
* Print queues: `/var/spool`
* Temporary files: `/var/tmp`

The `/var` directory may be put on its own filesystem so that the growth of the files can be accommodated and any exploding file sizes do not fatally affect the sysetm.
`/var` also contains network services directories such as `/var/ftp` (FTP) and `/var/www` (the HTTP web service).

### The /etc Directory

The `/etc` directory is the home for system configuration files.
It contains no binary programs, although there are some executable scripts.
For example, `/etc/resolv.conf` tells the system where to go on the network to obtain host name to IP address mappings (DNS).
Files like `passwd`, `shadow`, and `group` for managing user accounts are also found in the `/etc` directory.
While some distros have historically had their own extensive infrastructure under `/etc` (for example, Red Hat and SUSE have used `/etc/sysconfig`), with the advent of systemd there is much more uniformity among distros today.

Note that `/etc` is used for system-wide configuration files and only the superuser can modify files there.
User-specific configuration files are always found under their home directory.

### The /boot Directory

The `/boot` directory contains the few essential files needed to boot the system.
For each alternative kernel installed on the system there are four file:

* `vmlinuz`: The compressed Linux kernel, required for booting.
* `initramfs`: The initial ram filesystem, required for booting, sometimes called initrd
* `config`: The kernel configuration file, only used for =debugging and bookkeeping
* `System.map`: Kernel symbol table, only used for debugging.

Each of these files has a kernel version appended to its name.

The Grand Unified Bootloader (GRUB) files such as `/boot/grub/grub.conf` or `/boot/grub2/grub2.cfg` are also foundunder the `/boot` directory.

Names will vary and tend to look somewhat different across different distros.

### The /lib and /lib64 Directories

The `/lib` directory contains libraries (common code shared by applications and needed for them to run) for the essential programs in `/bin` and `/sbin`.
These library filenames start with either `ld` or `lib`, e.g. `/lib/libncurses.so.5.9`.

Most of these are what is known as dynamically-loaded libraries, also known as shared libraries or Shared Objects (SO).
On Some Linux distros there exists a `/lib64` directory containing 64-bit libraries, while `/lib` contains the 32-bit versions.

On recent Linux distros, one finds that, just like for `/bin` and `/sbin`, the `/lib` and `/lib64` directories just point to those under `/usr`: `/usr/lib` and `/usr/lib64`.

Kernel modules (kernel code, often device drivers, that can be loaded and unloaded without restarting the system) are located in `/lib/modules/<kernel-version-number>`.

### Removable Media: the /media, /run, and /mnt Directories

One often uses removable media, such as USB drives, CDs, DVDs, etc.
To make the material accessible through the regular filesystem, it must be mounted at a convenient location.
Most Linux systems are configured so any removable media are automatically mounted when the system registers that something has been plugged in.

While historically these were mounted under the `/media` directory, modern Linux distros place these mount points under the `/run` directory.
For example, a USB drive with a label `myusbdrive` for a user name `student` would be mounted at `run/media/student/myusbdrive`.

The `/mnt` directory has been used since the early days of UNIX for temporarily mounting filesystems.
These can be those on removable media, but more often might be network filesystems, which are not normally mounted.
Or these can be temporary partitions, or so-called **loopback** filesystems, which are files which pretend to be partitions.

### Additional Directories Under /

* `/opt`: Optional application software packages
* `/sys`: Virtual pseudo-filesystem giving information about the system and hardware.
Can be used to alter system parameters and for debugging purposes.
* `/tmp`: Temporary files; on some distros, `/tmp` is erased on reboot and/or may actually be a ramdisk in memory.
* `/usr`: Multi-user applications, utilities, and data

### The /usr Directory Tree

The `/usr` directory tree contains theoretically non-essential programs and scripts, in the sense that they should not be needed to initially boot the system.
The `/usr` directory tree has at least the following sub-directories:

* `/usr/include`: Header files used to compile applications
* `/usr/lib`: Libraries for programs in `/usr/bin` and `/usr/sbin`
* `/usr/lib64`: 64-bit libraries for 64-bit programs in `/usr/bin` and `/usr/sbin`
* `/usr/sbin`: Non-essential system binaries, such as system daemons
* `/usr/share`: Shared data used by applications, generally architecture-independent
* `/src/src`: Source code, usually for the Linux kernel
* `/usr/local`: Data and programs specific to the local machine.
Subdirectories include `bin`, `sbin`, `lib`, `share`, `include`, etc.
* `/usr/bin`: The primary directory of executable commands on the system

## Comparing Files and File Types

### Comparing Files with diff

`diff` is used to compare files and directories, with many useful options (see `man diff`) including:

* `-c`: Provides three lines of context before and after the differing lines
* `-r`: Recursively compare subdirectories including the current directory
* `-i`: Ignore case differences
* `-w`: Ignore whitespace differences
* `-q`: Quiet - report if files are different, but do not list the differences

```
$ diff [options] <filename1> <filename2>
```

`diff` if meant for use with text files; for binary files use `cmp`.

### Using diff3 and patch

`diff3` compares three files at once using one file as the reference basis for the other two.

```
$ diff3 [options] <filename1> <reference-file> <filename2>
```

Modifications to source code and configuration files are often distributed utilizing patches, which are applied with the `patch` program.
A patch file contains the deltas (changes) required to update an older version of a file to the new one.

The patch files are produced by running `diff` with the correct options, as in:

```
$ diff -Nur originalfile newfile > patchfile
```

Distributing only the patch ismore concise and efficient than distributing the entire file.
For a one-line change in a file that contains 1000 lines, the patch file will be just a few lines long.

To apply a patch, use one of the two methods below:

```
$ patch -p1 < patchfile
$ patch originalfile patchfile
```

The first usage is more common as it is often used to apply changes to an entire directory tree rather than just one file, as in the second example.
To understand the use of the `-p1` option and others, see `man patch`.

### Using the file utility

In Linux, a file's extension does not categorize it the way it might in other operating systems - one cannot assume that `file.txt` is a text file and not an executable program.
In Linux, a filename is generally more meaningful to the user of the system than to the system itself.
Most applications directly examine a file's contents to see what kind of object it is, rather than relying on an extension.

The real nature of a file can be ascertained using the `file` utility. For the filenames given as arguments, it examines the contents and characteristics to determine whether the files are plain text, shared libraries, executable programs, scripts, etc.
