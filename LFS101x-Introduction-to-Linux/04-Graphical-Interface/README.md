# Graphical Interface

## Learning Objectives

* Manage graphical interface sessions
* Perform basic operations using the graphical interface
* Change the graphical desktop to suit your needs

## Graphical Desktop

### X Window System

In a Linux desktop system, the X Window System is loaded as one of the final steps in the boot process.
It is often just called X.

A service called the **Display Manager** keeps track of the displays being provided and loads the X server (so called because it provides graphical services to applications, sometimes called X clients).
The display manager also handles graphical logins and starts the appropriate desktop environment after a user logs in.

X is very old, dating back to the 1980s, and as such has certain deficiencies.
The Wayland system is gradually superseding X.

### More About X

A desktop environment consists of a session manager, which starts and maintains the components of a graphical session, and the window manager, which controls the placement and movement of windows, window title bars, and controls.

Although they can be mixed and matched, generally a set of utilities, a session manager, and a window manager are used together as a unit to provide a seamless desktop environment.

If the display manager is not started by default, you can start the graphical desktop by running `startx` from the text-mode command line, or start the display manager directly (gdm, kdm).

### GUI Startup

When a desktop environment is installed, the X display manager starts at the end of the boot process.
It is responsible for starting the graphics system, logging in the user, and starting the user's desktop environment.
If multiple desktop environments are installed, you can select one when logging in.
