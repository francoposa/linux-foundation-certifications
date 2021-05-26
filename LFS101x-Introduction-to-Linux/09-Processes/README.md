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

The load average is displayed using three numbers, corresponding to the average usage over the previous one, five, and fifteen minutes.

In a single-CPU system, a value of 1.00 means the system was 100% utilized over the past n minutes.
A value over 1.00 for a single-CPU system implies that the system was over-utilized: there were more processes needing CPU than CPU was available.

In a multi-CPU system, divide the load averages by the number of CPUs.
For a quad-CPU system, a one-minute load average of 4.00 implies that the system as a whole was 100% utilized on average during the last minute.

Short-term spikes are usually not a problem, as process start and then activity settles down.
If a high load average is seen in the five- and fifteen-minute load averages, it may be cause for concern.

### Background and Foreground Processes

Linux supports background and foreground job processing.
A job in this context is just a command launched from a terminal window.
Foreground jobs run directly from the shell and any other foreground jobs for the same terminal must wait for shell access until the current job is completed.

By default, all jobs are executed in the foreground.
To free the shell for other tasks, you can run jobs in the background.
Background jobs will be executed at lower priority, which in turn allows smoother execution of the interactive tasks continuing in the terminal.
You can put a job in the background by suffixing `&` to the command, as in `updatedb &`.

`CTRL-Z` suspends a foreground job, while `CTRL-C` terminates a foreground job.
`bg` and `fg` commands can be used to manage which processes are running in the background and foreground, respectively.

### Managing Jobs

The `jobs` utility displays all jobs running in the background, with job ID, state, and command name.

`jobs -l` adds the PID of the background jobs to the output.

The jobs shown are only those that are child processes of the current shell, that is, those that you have launched and sent to the background from the current shell session.

## Listing Processes: ps and top

### The ps Command (System V Style)

`ps` provides information about currently running processes, keyed by PID.
If you want a continually-updated view of this status, you can use `top` or its commonly-installed variants such as `htop` or `atop` from the command line, or use your distro's graphical system monitor application.

`ps` has extensive options for specifying which tasks to examine, which information to display, and in which output format to display.

Without options, `ps` will display all processes running under the current shell.
The `-u` option displays processes for a specified username.
`ps -ef` displays all processes in the system in full detail.
`ps -eLf` foes one step further and displays one line of information for each thread (remember a process can have multiple threads)

### The ps Command (BSD Style)

`ps` has another style of option specification which stems from the BSD variety of UNIX, where options are specified without preceding dashes.

For example, `ps aux` displays all processes of all users. `ps axo` allows you to specify which attributes to view, as in `ps axo stat,priority,pid,pcpu,comm`.

### The Process Tree

`pstree` displays the processes running on the system in the form of a tree diagram showing the relationship between a process, its parent process, and any other processes that it created.
Repeated entries of a process are not displayed, and threads are displayed in curly braces.

### top

Instead of running `ps` repeatedly, `top` offers a continually-updated view of processes and system utilization, and allows you to highlight which processes are consuming the most CPU cycles and memory

### First Line of the top Output

The first line of `top` output displays a quick summary of what is happening in the system, including:

* How long the system has been up
* How many users are logged on
* What the load average is

If the load average goes above 1.00 per CPU, it indicates processes are competing for CPU time.
If the load average is very high, it may indicate a problem such as a runaway process.

### Second Line of the top Output

The second line of `top` output displays the total number of processes, the number running, sleeping, stopped, and zombie processes.

Comparing the running processes with the load average can help determine if the system has reached capacity or perhaps a particular user is running too many processes.
Stopped processes should be examined to see if everything is running correctly.

### Third Line of the top Output

The third line of `top` output indicates how the CPU time is being divided between the users (`us`) and the kernel (`sy`) by displaying the percentage of CPU time used for each.

The percentage of user jobs running at a lower priority (`niceness - ni`) is then listed.
Idle mode (`id`) should low if the load average is high, and vice versa.
The percentage of jobs waiting (`wa`) for I/O is listed.
Interrupts include the percentage of hardware (`hi`) vs software (`si`) interrupts.
Steal time (`si`) is generally used with virtual machines which have some of their idle CPU time taken for other uses.

### Fourth and Fifth Lines of the top Output

The fourth and fifth lines of `top` output indicate memory usage, which is divided into two categories:

* Physical memory (RAM) - displayed on line 4
* Swap space - displayed on line 5

Both categories display total memory, used memory, and free space.

You will need to monitor memory usage carefully to ensure good system performance.
Once the physical memory is exhausted, the system starts using swap space (temporary storage space on the hard drive) as an extended memory pool.
Since disk access is much slower than memory access, this will negatively affect system performance.

### Process List of the top Output

By default, the `top` output process list orders the process by highest CPU usage.
The following information about each process is displayed:

* Process Identification Number (`PID`)
* Process owner (`USER`)
* Priority (`PR`) and nice values (`NI`)
* Virtual (`VIRT`), physical (`RES`), and shared (`SHR`) memory
* Status (`S`)
* Percentage of CPU (`%CPU`) and memory (`%MEM`) used
* Execution time (`TIME+`)
* Command (`COMMAND`)

### Interactive Keys with topic

`top` can be used interactively for monitoring and controlling processes.
While `top` is running in a terminal window, you can enter single-letter commands to change its behavior.
For example, you can specify the sort order for the process list, alter the priorities of a running process, or stop/kill a process.

Some commands include:

* `t`: Display or hide summary info (rows 2 and 3)
* `m`: Display or hide memory info (rows 4 and 5)
* `A`: Sort the process list by top resource consumers
* `r`: Renice (change the priority of) a specific process
* `k`: Kill a specific process
* `f`: Enter the `top` configuration screen
* `o`: interactively select a new sort order for the process list


