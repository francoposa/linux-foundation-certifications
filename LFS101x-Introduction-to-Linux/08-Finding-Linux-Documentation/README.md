# Finding Linux Documentation

## Documentation Sources

### Linux Documentation Sources

Important Linux documentation sources include:

* man pages (short for manual pages)
* GNU Info
* the `help` command and `--help` option
* other documentation sources e.g. Gentoo Handbook or Ubuntu Documentation

### The man pages

The man pages are the most often-used source of Linux documentation.
They provide in-depth documentation about many programs and utilities, as well as other topics including configuration files and programming APIs for system calls, library routines, and the kernel.
man pages were first introduced in the early UNIX versions in the 1970s and are present on all Linux distros.

Typing `man` with  a topic name as an argument retrieves the information stored in the topic's man pages.

man pages are often converted to other formats, such as PDFs and web pages.

### man

The **man** program searches, formats, and displays the information contained in the man page system.
Because many topics have copious amounts of relevant information, output is piped through a pager program such as `less` to be viewed one page at a time.
As it is piped through the pager program, the information is formatted for visual display.

A given topic may have multiple pages associated with it, and there is a default order determining which one is displayed when no options or section number is specified.
To list all pages on the topic, use the `-f` option.
To list all pages that discuss a specified topic, even if the specified subject is not present in the name, use the `-k` option.

`man -f` generates the same result as `whatis`.

`man -k` generates the same result as `apropos`.

The default order is specified in `/etc/man_db.conf` and is roughly (but not exactly) in ascending numerical order by section.

### Manual Chapters

The man pages are divided into chapters numbered 1 through 9.
In some cases, a letter is appended to the chapter number to identify a specific topic.
For example, many pages describing part of the X Window API are in chapter 3X.

The chapter number can be used to force man to display the page from a particular chapter.
It is common to have multiple pages across multiple chapters with the same name, particularly for names of library functions or system calls.

With the `-a` parameter, man will display all pages with the given name in all chapters, one after the other, as in:

```
$ man -a socket
```

## GNU Info

### The GNU Info System

The **GNU Info** System is the GNU project's standard documentation format, which it prefers as an alternative to **man**.

Functionally, **info** resembles man in many ways.
However, topics are connected using links, even though its design predates the world wide web.
Information can be viewed via a command line interface, a graphical help utility, or viewed online.

### Using info from the Command Line

Typing `info` with no arguments into a terminal will display an index of available topics.

View help for a particular topic by typing `info <topic name>`.
The system will search for the topic in all available `info` files.

Useful keys: `q` to quit, `h` for help, and `Enter` to select a menu item.

### info Page Structure

The topic which you view in an info page is called a **node**.

Nodes are essentially sections and subsections in the documentation.
You can move between nodes or view each node sequentially.
Each node may contain menus and linked subtopics, or items.

Items function like browser links and are identified by an asterisk (*) at the beginning of the item name.
Named items outside a menu are identified with double colons (::) at the end of the item name.
Items can refer to other nodes within the file or to other files.

* `n`: Go to the next node
* `p`: Go to the previous node
* `u`: Move up one node in the index

## The --help Option and help Command

### The --help Option

Most commands have an available short description which can be viewed using the `--help` or `-h` option.
For example:

```
$ man --help
```

The `--help` option is useful as a quick reference and it displays information faster than the **man** or **info** pages.

### The help Command

When run within a bash command shell, some popular commands (such as `echo` and `cd`) actually run specially built-in bash versions of the commands rather than the usual binaries found on the file system, say under `/bin` or `/usr/bin`.
If is more efficient to do so as execution is faster because fewer resources are used.
One should note that there can be some (usually small) differences in the two versions of the command.

To view a synopsis of these built-in shell commands, you can just type `help` into the terminal.

Type `help <command>` to view the full information for a specific command.

For these built-in shell commands, `help` performs the same basic function as the `-h` and `--help` arguments perform for standalone programs.
