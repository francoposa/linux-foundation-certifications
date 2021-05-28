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

The `/` directory may be put on its own filesystem so that the growth of the files can be accommodated and any exploding file sizes do not fatally affect the sysetm.
`/var` also contains network services directories such as `/var/ftp` (FTP) and `/var/www` (the HTTP web service).

### The /etc Directory

The `/etc` directory is the home for system configuration files.
It contains no binary programs, although there are some executable scripts.
For example, `/etc/resolv.conf` tells the system where to go on the network to obtain host name to IP address mappings (DNS).
Files like `passwd`, `shadow`, and `group` for managing user accounts are also found in the `/etc` directory.
While some distros have historically had their own extensive infrastructure under `/etc` (for example, Red Hat and SUSE have used `/etc/sysconfig`), with the advent of systemd there is much more uniformity among distros today.

Note that `/etc` is used for system-wide configuration files and only the superuser can modify files there.
User-specific configuration files are always found under their home directory.
