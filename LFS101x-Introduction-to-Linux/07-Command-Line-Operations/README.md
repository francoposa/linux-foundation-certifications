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

*Sudo* may need to be set up to work correctly, though many distros will set it up properly for you during installation.

### Steps for Setting Up and Running Sudo

If your system does not already have sudo set up and enabled, you will need to do the following steps:

1.
