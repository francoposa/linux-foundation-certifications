# System Configuration from the Graphical Interface

* Apply system, display, and date and time settings using the *System Settings* panel.
* Track the network settings and manage connections using *Network Manager* in Linux
* Install and update software in Linux from a graphical interface

## System, Display, Date and Time Settings

### Display Settings

The X server, which actually provides the GUI, uses `/etc/X11/xorg.conf` as its configuration file *if it exists*.
In modern Linux distros, this file is usually present only in unusual circumstances, such as when less-common graphic drivers are in use.
Changing this configuration file is usually for more advanced users.

### Date and Time Settings

By default, Linux always uses Coordinated Universal Time (UTC) for its own internal timekeeping.
Displayed or stored time values rely on the system time zone setting to get the proper time.
UTC is similar to, but more accurate than, Greenwich Mean Time.

### Network Time Protocol

The **Network Time Protocol (NTP)** is the most popular and reliable protocol for setting the local time by consulting established internet servers.
Linux distros always come with a working NTP setup, which refers to specific time servers run or relied on by the distro.
This means no setup is required for network time synchronization.
If desired, more detailed configuration is possible by editing the standard NTP configuration file `/etc/ntp.conf`.

## Installing and Updating Software

### Installing and Updating Software

All systems have a lower-level utility which handles the details of unpacking a package and puting the pieces in the right places.
Most of the time, you will be working with a higher-level utility which knows how to download packages from the internet and can manage dependencies and groups for you.

### Debian Packaging

**Dpkg** is the underlying package manager for these systems.
It can install, remove, and build packages.
Unlike higher-level package management systems, it does not automatically download and install packages and satisfy their dependencies.

For Debian-based systems, the higher-level package management system is the **Advanced Package Tool (APT)** system of utilities.
Generally while each distro within the Debian family uses APT, it creates its own user interface on top of it (apt, apt-get, aptitude, synaptic, Ubuntu Software Center, Update Manager, etc.)
Although apt repositories are generally compatible with each other, the software they contain is generally  not.
Therefore, most repositories target a particular distro, and often software distributors ship with multiple repositories to suppoer multiple distros.

### Red Hat Package Manager (RPM)

**Red Hat Package Manager (RPM)** is the other package management system popular on Linux distros.
It was developed by Red hat and adopted by a number of other distros, including SUSE/openSUSE, Mageia, CentOS, Oracle Linux, and others.

The higher-level package manager differs between distros: Red Hat family distros historically used yum, however RHEL/CentOS and Fedor have moved over to using dnf, which is mostly backwards compatible with yum.
SUSE family distros also use RPM, but with the zypper interface.
The GNOME project also uses PackageKit as a unified interface.
