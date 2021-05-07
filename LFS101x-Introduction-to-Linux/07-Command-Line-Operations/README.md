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

`$ sudo systemctl stop gdm` or `sudo telint 3`

`$ sudo systemctl start gdm` or (`sudo telint 5`)

## Basic Operations
