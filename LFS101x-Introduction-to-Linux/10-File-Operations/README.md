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
