# Command Line Operations

## Command-Line Mode Options

### Introduction to the Command Line

"Graphical user interfaces make easy tasks easier, command line interfaces make difficult tasks possible."
Linux relies heavily on an abundance of command line tools.
Command line interfaces provide the following advantages:

* No GUI overhead is incurred
* Virtually any task can be accomplished without leaving the command line
* Scripts can be used to group together common series of tasks and procedures
* You can sign into remote machines anywhere on the internet
* Graphical applications can be launched directly from the command line instead of hunting through menus
* While graphical tools vary among Linux distros, the commmandline interface does not.

### Using a Text Terminal on the Graphical Desktop

A **terminal emulator** simulates a standalone terminal within a window on the desktop, meaning it behaves essentially as if you were logging into the machine at a pure text terminal with no running GUI.
Most terminal emulators support multiple terminal sessions via tabs or windows.

Some terminal emulators include:

* gnome-terminal (default on GNOME)
* xterm
* rxvt
* konsole (default on KDE)
* terminator

### Some Basic Utilities

* `cat`: type out a file or combine files
* `head`: show the first few lines of a file
* `tail`: show the last few lines of a files
* `man`: view documentation

The pipe symbol `|` is used to pipe the output of one program into the input to another.

### The Command Line

Most input lines entered at the shell prompt have three basic elements:

* Command: the name of the program to execute
* Options: flags that modify what the command may do, usually starting with one or two dashes (ex. `-p` or `--print`)
* Arguments: what the command operates on

Many commands will have no options, no arguments, or neither.
In addition, other elements such as setting environment variables can also appear on the command line when launching a task.

### Sudo

**Sudo** allows users to run programs using the security privileges of another user, generally root/superuser.

**Sudo** may need to be set up to work correctly, though many distros will set it up properly for you during installation.

### Steps for Setting Up and Running Sudo

If your system does not already have sudo set up and enabled, you will need to do the following steps:

1. You will need to make modifications as the administrative or superuser root. While **sudo** will become the preferred method of doing this, it is not set up yet, so we will use **su** instead. At the command prompt, type `su` and press Enter. You will be prompted for the root password, so enter it and press Enter. You should end up with a different-looking command  prompt, often ending with `#`.
2. Now, create the configuration file to enable your user to user sudo. Typically, this file is created in the `etc/sudoers.d/` directory with the name of the file the same as your username. For example, if your username is `student`, you would create the configuration file for student by entering `# echo "student ALL=(ALL) ALL" > /etc/sudoers.d/student`
3. Finally, some Linux distros will complain if you do not also change permissions on the file: `# chmod 440 /etc/sudoers.d/student`

Sudo should now be properly set up.

When using sudo by default you will be prompted to give a your own user passowrd, at least the first time you use sudo within a specified time period.
It is possible, though insecure, to configure sudo to not require a password, or to change the time window in which the password does not have to be repeated with every sudo command.

### Switching Between the GUI and the Command Line

Linux can run with or without the GUI, and switch back and forth between modes.

Most Linux distros have installation options for desktop (with GUI) and server (without GUI).

Linux production servers are usually installed without the GUI, and if it is installed, the GUI is not launched on system startup. Removing the GUI from a production server is helpful in maintaining a lean system, which can be easier to support and keep secure.

### Virtual Terminals

**Virtual Terminals (VT)** are console sessions that use the entire display and keyboard, outside of a graphical environment.
Such terminals are considered "virtual" because although there can be multiple active terminals, only one terminal remains visible at a time.

One virtual terminal, usually number 1 or 7, is reserved for the graphical environment, and text logins are enabled on the other unused VTs.
Ubuntu uses VT 7 for the graphical session, but CentOS/RHEL and openSUSE use VT 1.

If you run into issues with the graphical desktop, you can switch to one of the text VTs to troubleshoot.

To switch between VTs, press `CTRL+ALT+function key` for the VT.

### Turning Off the Graphical Desktop

Linux distros can start and stop the graphical desktop in various ways.
The exact method differs among distros and distro versions.
For newer systemd-based distros, the display-manager is run as a service so you can stop the GUI with the systemctl utility.
Most distros with also work with the `telint` command.

```
$ sudo systemctl stop gdm # or
$ sudo telint 3
```

```
$ sudo systemctl start gdm # or
$ sudo telint 5
```

## Basic Operations

### Logging In and Out

An available text terminal will prompt for a username with the string `login:` and password.
When typing your password, nothing is displayed in the terminal to prevent others from seeing your password.

Once your session is started by logging in to a text terminal or graphical terminal program, ou can also connect and log into remote systems using **Secure SHell (SSH)**

### Rebooting and Shutting Down

The preferred method to shut down or reboot the system is to use the `shutdown` command.
This sends a warning message and then prevents ruther suers from logging in.
The init process will then control shutting down or rebooting the system.
It is important to always shut down properly; failure to do so can result in damage to the system and/or loss of data.

The `halt` and `powerof` commands issue `shutdown -h` to halt the system; `reboot` issues `shutdown -r` to reboot.
Both shutting down and rebooting from the command line require superuser (root) access.

When administering a multi-user system, you have the option of notifying all  users prior to shutdown:

```
$ sudo shutdown -h 10:00 "Shutting down for scheduled maintenance"
```

**Note**: On recent Wayland-based Linux distros, broadcast messages do not appear on terminal emulation sessions running on the desktop; they appear only in the VT console displays.


### Locating Applications

Depending on the specifics of a distro's policy, programs and packages can be installed in various directories.
In general, executable programs and scripts should live in then `/bin`, `/usr/bin`, `/sbin`, `/usr/sbin` directories, or somewhere under `/opt`.
They can also appear in `/usr/local/bin`, `/usr/local/sbin`, or in a a directory in a user's account space, such as `/home/student/bin`.

One way to locate programs is to employ the `which` utility.

```
$ which diff
/usr/bin/diff
```

If `which` does not find the program, `whereis` is a good alternative because it looks for packages in a broader range of system directories:

```
$ whereis diff
diff: /usr/bin/diff /usr/share/man/man1/diff.1.gz /usr/share/man/man1p/diff.1p.gz
```

as well as locating source and man files packaged with the program.

### Accessing Directories

When you first log into a system or open a terminal, the default directory should be your home directory.
You can print the exact path of this with the command `echo $HOME`.
Many Linux distros actually open new graphical terminals in `$HOME/Desktop`.

The following commands are useful for directory navigation:

* `pwd`: Displays the present working directory
* `cd ~` or `cd`: Change to home directory
* `cd ..`: Change to parent directory
* `cd -`: Change to previous directory


### Understanding Absolute and Relative Paths

There are two ways to identify paths:

* **Absolute pathname**: An absolute pathname begins with the root `/` and follows the tree branch by branch until it reaches the desired directory or file.
* **Relative pathname**: starts from present directory, either with `./` or no leading slash at all.
Can also start from another directory which can be referred to by shorthand, such as `../` for parent directory or `~` for home directory.


Multiple slashes between directories and files are allowed, but all but one slash between the elements will be ignored by the system.
`////usr/bin` is valid, but is seen as `/usr/bin` by the system.

### Exploring the filesystem

Traversing up and down the filesystem tree can get tedious.
The `tree` command is a good way to get a bird's-eye view of the filesystem.
User `tree -d` to view just the directories and to suppress listing file names.

The following commands can help in exploring the filesystem:

* `cd /`: Change directory to the root directory or the path you supply
* `ls`: List the contents of the present working directory
* `ls -a`: List all the files including hidden files (dotfiles) and directories
* `tree`: Displays a tree view of the filesystem


### Hard Links

The `ln` utility is used to create hard links and (with the `-s` option) softlinks, also known as symbolic links or symlinks.
These two kinds of links are very useful in UNIX-based operating systems.

Supposed that `file1` already exists.
A hard link, called `file2`, is created with the command

```
$ ln file1 file2
```

Note that two files now appear to exist.
However, a closer inspection of the file listing shows that this is not quite true.

```
$ ls -li file1 file2
```

The `-i` option to `ls` prints out in the first column the inode number, which is a unique quantity for each file object.
This field is the same for both of these files; what is really going on here is that it is only one file, but it has more than one "name" associated with it, as is indicated by the `2` that appears in the `ls` output.

Hard links are very useful and they save space, but you have to be careful with their use, sometimes in subtle ways.
For example, if remove either `file1` or `file2` in the example, the inode object as well as the remaining file name will stay, which may lead to subtle errors later if you recreate a file of that name.

If you edit one of the files, exactly what happens depends on your editor; most editors, including vi and gedit, will retain the link by default, but it is possible that modifying one of the names may break the link and result in the creation of two objects.


### Soft (Symbolic) Links

Soft or Symbolic links are created with the `-s` option to `ln`:

```
$ ln -s file1 file3
$ ls -li file1 file3
```

Notice `file3` no longer appears to be a regular file, and it clearly points to `file1` and has a different inode numbers.

Symbolic links take no extra space on the filesystem unless their names are very long.
They are very convenient, as they can easily be modified to point to different places.

Unlike hard links, soft links can point to objects on other filesystems, partitions, and/or disks and other media, which may or may not be currently available.
In the case where the link does not point to an existing or currently available file, you obtain dangling link.

### Navigating the Directory History

The `cd` command remembers where you were last and lets you go back with `cd -`.
For remembering more than just the last directory visited, use  `pushd` to change the directory instead of `cd`; this pushes your starting directory onto a stack.
Using `popd` will then send you back to those directories, walking in reverse order.
The list of directories is displayed with the dirs command.

## Working with Files

### Viewing Files

You can use the following command-line utilities to view files:

* `cat`: used for viewing files that are not very long; does not provide any scrollback
* `tac`: used to look at a file backwards, starting with the last line
* `less`: used to view larger files because it is a paging program.
It pauses at each screen full of text, provides scrollback capabilities, and lets you search and navigate within the file.
Use `/` to search forward and `?` to search backward.
An older program named `more` is still used, but has fewer capabilities: "less is more".
* `tail`: used to print the last 10 lines of a file by default.
Specify the number of lines with `-n [number]` or just `-[number]`.
* `head`: opposite of `tail`; prints first 10 lines of a file by default

### touch

`touch` is often used to set or update the accessed changed, and modified timestamps of files.
By default, it resets a file's timestamp to match the current time.

However, you can also create an empty file using `touch`:

```
$ touch [filename]
```

`touch` provides several useful options.
For example, the `-t` option allows you to set the date and timestamp of the file to a specific value, as in:

```
$ touch -t 12091600 [filename]
```

This sets the file's timestamp to 4 PM, December 9th (12 09 1600).

### mkdir and rmdir

`mkdir` is used to create a directory:

* `mkdir [directory name]` creates the directory under the current directory
* `mkdir [some absolute or relative path]/[directory name]` creates the directory under the specified paths

`rmdir` removes empty directories.
To remove a non-empty directory with all of its comments, you must use `rm -rf`.

### Moving, Renaming, or Removing a Files

The `mv` command can simply rename a file, or move the file to another location, optionally changing its name as well.

If you are not certain about removing files that match a pattern you supply, you can run `rm` interactively (`rm -i`) to prompt before each removal.

* `mv`: Rename a file
* `rm`: Remove a file
* `rm -f`: Forcefully remove a file
* `rm -i`: interactively remove a file

### Renaming or Removing a Directory

`rmdir` works only on empty directories; otherwise you get an error.

While typing `rm -rf` is a fast and easy way to remove a whole filesystem tree recursively, it is extremely dangerous and should be used with the utmost care, especially when used by root.

* `mv`: Rename a directory
* `rmdir`: Remove an empty directory
* `rm -rf`: Forecully remove a directory recursively

### Modifying the Command Line Prompt

The `PS1` variable is the character string that is displayed as the prompt on the command line.
Most distros set `PS1` to a known default value which is suitable in most cases.
For example, some system administrators require the user and host system name to show up on the command line as in:

```
student@lab1-c3 $
```

This can prove useful if you are working in multiple roles and want to be reminded of who you are and which machine you are on.
The prompt above could be implemented for a bash shell by setting `PS1='\u@\h \$'`.

By convention, most systems are set up so that the root user has a pound sign (`#`) as their prompt.


## Searching for Files

### Standard File Streams

When commands are executed, by default there are three standard file streams (or descriptors) always open for use:

* standard input (stdin)
    * value 0
    * ex: keyboard
* standard output (stdout)
    * value 1
    * ex: terminal
* standard error (stderr)
    * value 2
    * ex: log file

Usually, `stdin` is your keyboard, and `stdout` and `stderr` are printed on your terminal.
`stderr` is often redirected to an error logging file, while `stdin` is supplied by redirecting input to come from a file or from the output of a previous command through a pipe.
`stdout` is also often redirected into a file.
Nothing should be written to `stderr` during normal execution of a successful process.

In Linux, all open files are represented internally by what are called file descriptors.
Simply put, these are represented by numbers starting at zero.
`stdin` is file descriptor 10, `stdout` is file descriptor 1, and `stderr` is file descriptor 2.
Typically, if other files are opened in addition to these three, which are opened by default, they will start at file descriptor 3 and increase from there.

### I/O Redirection

Through the command shell, we can redirect the three standard file streams so that we can get input from either a file or another command, instead of from our keyboard, and we can write output and errors to files or use the to provide input for subsequent commands.

For example, if we have a program called `do_something` that reads from `stdin` and writes to `stdout` and `stderr`, we can change its input source by using the less-than sign (`<`) followed by the name of the file to be consumed for input data:

```
$ do_something < input-file
```

If you want to send the `stdout` output to a file, use the greater-than sign (`>`) as in:

```
$ do_something > output-file
```

Because `stderr` is not the same as `stdout`, error messages will still be seen on the terminal windows in the above example.

If you want to redirect `stderr` to a separate file, you use `stderr`'s file descriptor number (2), the greater-than sign (`>`), followed by the name of the file:

```
$ do-something 2> error-file
```

By the same logic, `do_something 1> output-file` is the same as `do_something > output-file`.

A special shorthand notation can send anything written to file descriptor 2 (`stderr`) to the same place as file descriptor 1 (`stdout`): `2>&1`.

```
$ do_something > all-output-file 2>&1
```

Bash permits an easier syntax for the above:

```
$ do_something >& all-output-files
```

### Pipes

The UNIX/Linux philosophy is to have many simple and short programs or commands cooperate together to produce complex results, rather than have one complex program with many possible options and modes of operation.
In order to accomplish this, extensive use of pipes is made.
You can pipe the output of one command or program into another as its input.

In order to do this we use the vertical bar (`|`) (pipe symbol) beween commands:

```
$ command1 | command2 | command3
```

The above represents what we often call a pipeline, and allows Linux to combine the actions of several commands into one.
This is extraordinarily efficient because `command2` and `command3` do not have to wait for the previous pipeline commands to complete before they can begin working on the data in their input streams.
On multi-CPU or multi-core systems, the available computing power is much better utilized and things get done quicker.

Further, there is no need to save output in temporary files in between the stages of the pipeline, which saves disk space and reduces reading and writing to disk, which is often the slowest bottleneck in getting something done.

### locate

The `locate` utility program performs a search taking advantage of a previously constructed database of files and directories on your system, matching all entries that contain a specified character string.
This can result in a very long list.

To get a shorter and possible more relevant list, we can use the `grep` program as a filter.
`grep` will print only the lines that contain one of more specified strings, as in:

```
$ locate zip | grep bin
```

which will list all the files and directories with both "zip" and "bin" in their name.

`locate` utilizes a database created by a related utility, `updatedb`.
Most Linux systems run this automatically once a day.
However, you can update it any time by just running `updatedb` from the command line as the root user.

### Wildcards and Matching File Names

You can search for a filename containing specific characters using wildcards.

* `?`: Matches any single character
* `*`: Matches any string of characters
* `[set]`: Matches any character in the set of characters
* `[!set]`: Matches any character not in the set of characters

### The find Program

`find` recurses down the filesystem tree from any particular directory or set of directories and locates files that match specified conditions.
The default pathname is always the current working directory.

System adminstrators sometimes scan for potentially large core files (which contain diagnostic information after a program fails) that are more than several weeks old in order to remove them.

It is also common to remove files in inessential or outdated files in `tmp` (and other volatile directories, such as those containing cached files) that have not been accessed recently.
Many Linux distros use shell scripts that run periodically (through `cron` usually) to perform such house cleaning tasks.

### Using find

When no arguments are given, `find` lists all files in the current directory and all of its subdirectories.
Commonly used option to shorten the list include:

* `-name`: Only list files with a certain pattern in their name
* `-iname`: Like `-name`, but ignoring the case of file names
* `-type`: Restrict results to files of a specified type, such as `d` for directory, `l` for symbolic link, or `f` for a regular file, etc.


Searching for files and directories named "gcc":

```
$ find /usr -name gcc
```

Searching only for directories named "gcc":

```
$ find /usr -type d -name gcc
```

Searching only for regular files named "gcc":

```
$ find /usr -type f -name gcc
```

### Using Advanced find Options

Another use of find is the ability to run commands on the files that match your search criteria.
The `-exec` options is used for this purpose.

To find and remove all files that end with `.swp`:

```
$ find -name "*.swp" -exec rm {} ';'
```

The `{}` (squiggly brackets) is a placeholder that will be filled with each of the file names that result from the find expression and the command will be run on each one individually.

Note that you must end the command with either `';'`, including the single quotes, or `\;`.
Both forms are fine.

One can also use the `-ok` option, which behaves the sames `-exec`, except that `find` will prompt you for permission before executing the command.
This is a good way to test your results before blindly executing any potentially dangerous commands.


### Finding Files Based on Time and Size

To find files based on time:

```
$ find / -ctime 3
```

Here, `ctime` is when the inode metadata (file ownership, permissions, etc.) last changed; it is often, but not necessarily, when the file was first created.
You can also search for accessed/last read( `-atime`) or modified/last written (`-mtime`) times.
The number is the number of days can be expressed as either a number number (`n`) that means exactly that value, `+n`, which means greater than that number, or `-n`, which means less than that number.
There are similar options for times in minutes: `-cmin`, `-amin`, and `-mmin`.

To find files based on size:

```
$ find / -size 0
```

Here the size is in 512-byte blocks, by default; you can also specify bytes (c) kilobytes(k), megabytes(M), gigabytes (G), etc.
As with the time numbers above, file sizes can also be exact numbers (`n`), `+n`, or `-n`.
For details, consult the man page for find.

For example, to find files greater than 10 MB in size and running a command on those files:

```
$ find / -size +10M -exec command {} \;
```


