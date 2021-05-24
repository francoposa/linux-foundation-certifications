# Processes

## Learning Objectives

* Describe what a process is and distinguish between types of processes
* Enumerate process attributes
* Manage processes using ps and top
* Understand the use of load averages and other process metrics
* Manipulate processes by putting them in background and restoring them to foreground
* Use `at`, `cron`, and `sleep` to schedule processes in the future or pause them

## Introduction to Processes and Process Attributes

### What Is a Process?

A **process** is simply an instance of one more related tasks (threads) executing on your computer.
It is not the same as a program or a command.
A single command may actually start several processes simultaneously.
Some processes are independent of each other, and others are related.
A failure of one process may or may not affect the others running on the system.

Processes use many system resources, such as memory, CPU cycles, and peripheral devices such as network cards, hard drives, printers, and displays.
The operating system, particularly the kernel, is responsible for allocating a proper share of these resources to each process and ensuring overall optimized system utilization

### Process Types

A terminal window (one kind of command shell) is a process that runs as long as needed.
It allows users to execute programs and access resources in an interactive environment.
You can also run programs in the background, which means they become detached from the shell.

Processes can be of different types according to the task being performed.

* Interactive Processes: need to be started by a user, either at a command line of graphical interface
    * ex: bash, firefox, top
* Batch Processes: Automatic processes which are scheduled from and then disconnected from the terminal.
These tasks are queued and work on a FIFO (First In First Out) basis.
    * ex: updatedb, ldconfig
* Daemons: Server processes that run continuously.
Many are launched during system startup, then wait for user or system requests.
    * ex: httpd, sshd, libvrtd
* Threads: Lightweight processes.
These are tasks that run under the umbrella of a main process, sharing memory and other resources, but are scheduled and by the system on an individual basis.
An individual thread can end without terminating the whole process, and a process can create new threads at any time. Many non-trivial programs are multi-threaded.
    * ex: firefox, gnome-terminal-server
Kernel Threads: Kernel tasks that users neither start nor terminate and have little control over.
These may perform actions like moving a thread from one CPU to another, or making sure I/O operations to disk are completed.
    * ex: kthreadd, migration, ksoftirqd

### Process Scheduling and States

A critical kernel function called the **scheduler** constantly shifts processes on and off the CPU, sharing time according to relative priority, how much time is needed and how much time has already been granted to a task.

When a process is in a **running** state, it means it is either currently executing instructions on a CPU, or is waiting to be granted a share of CPU time called a time slice so it can execute.
All processes in a running state reside on what is called a run queue and on a computer with multiple CPUs or cores, there is a run queue on each.

Processes can also go into a **sleep** state, generally when they are waiting for something to happen before they can resume, perhaps for the suer to type something.
In this condition, a process is said to be sitting on a wait queue.

There are other less frequent processes states, especially when a process is terminating.
Sometimes a child process completes but its parent process has not asked about its state.
Such a process is said to be in a zombie state - it is not really alive, but it still shows up in the system's list of processes.

### Process and Thread IDs

The operating system keeps track of processes being executed by assigning a unique process ID (**PID**) number.
The PIT is used to track processes state, CPU usage, memory use, where resources are located in memory, and other characteristics.

New PIDs are usually assigned in ascending order as processes are born.
Thus, PID 1 denotes the **init** process, and succeeding processes are gradually assigned higher numbers.

* Process ID (PID): Unique Process ID number
* Parent Process ID (PPID): Parent process that started this process.
If the parent dies, the PPID will refer to an adoptive parent; on recent kernels, this is kthreadd which has PPID=2.
* Thread ID (TID): Thread ID number.
This is the same as the PID for single-threaded processes.
For a multi-threaded process, each thread shares the same PID, but has a unique TID.

### Terminating a Process

To terminate a process, you can type `kill -SIGKILL <pid>` or `kill -9 <pid>`.

You can only kill your own processes; those belonging to another user are off limits unless you are root.

### User and Group IDs

Many users can access a system simultaneously, and each user can run multiple processes.
The operating system identifies the user who starts the process by the Real User ID (RUID) assigned to the user.

The user who determines the access rights for the user is identified by the Effective UID (EUID).
The EUID may or may not be the same as RUID.

Users can be categorized into various groups.
Each group is identified by the Real Group ID (RGID).
The access rights of the group are determined by the Effective Group ID (EGID).
Each user can be a member of one of more groups.

Most of the time we ignore these details and just talk about the User ID (UID) and Group ID (GID).

### More About Priorities

At any given time, many processes are running (i.e. in the run queue) on the system.
However, a CPU can actually accommodate only one task at a time.
Some processes are more important than others, so Linux allows you to set and manipulate process priority.
Higher priority processes get preferential access to the CPU.

The priority for a processes can be set by specifying a **nice value** or niceness, for the process.
The lower the nice value, the higher the priority.
Low values are assigned to important processes, while high vlaues are assigned to processes that can wait longer.
A process with a high nice value allows other processes to be executed first.
In Linux, a nice value of `-20` represents the highest priority and `+19` represents the lowest.

You can also assign a so-called **real-time priority** to time-sensitive tasks.
This is just a very high priority and is not to be confused with what is called "hard real time".
Hard real time is conceptually different and has to do with making sure a job gets completed within a very well-defined time window.

## Process Metrics and Process Control

### Load Averages

The **load average** is the average of the load number for a given period of time.
It takes into account processes that areL

* Actively running on a CPU
* Considered runnable, but waiting for a CPU to become available
* Sleeping: i.e. waiting for some kind of reasource (typically I/O) to become available

Linux differs from other UNIX-like operating systems in that it includes the sleeping processes.
Further, it only includes so-called uninterruptible sleepers, those which cannot be awakened easily.

The load average can be viewed by running `w`, `top`, or `uptime.`

### Interpreting Load Averages


