# Understanding Linux OS
The `kernel` is the main part of an Operating System as it allows the hardware and software components to interact as well as managing all the resources. The `Linux` kernel was developed by __Linus Torvalds__, a Finnish student in 1991 to pair with the UNIX like GNU to create a free open-source Operating System. Therefore, Linux is just the __kernel__ and __NOT__ the entire operating system. The system itself is made up of 3 parts:
1. __Hardware__: The physical components like CPU, memory, and storage devices.
2. __Linux Kernel__: Manages the hardware and facilitates the interaction of the software and hardware components of a computer.
3. __User Space__: The environment, like CLI where one can interact with the kernel.

There are many different types of Linux distributions or `distros` can be be used such as Debian, Ubuntu, and Linux Mint.

- [Command Line](#navigation-basics-and-the-command-line)
- [The Kernel](#the-kernel)
- [Text](#text-processing)
- [Process Management](#process-management)
- [Process Utilisation](#process-utilisation)
- [Filesystem](#filesystem)

## Navigation Basics and the Command Line
The `Shell` is a program that allows typed commands and send those commands to the OS to execute. Applications such as Terminals or Consoles open up a shell session when in use. Most Linux systems use the `Bourne Again Shell (Bash)` which offers many commands that the kernel can execute.

Linux treats everything like a file, therefore it is important to understand the hierarchy/structure of the filesystem to be able to navigate it well:
```code
'/': root directory (top level)
    '/bin': Important Binary Executables
    '/etc': Configuration Files
    '/home': User home directories (Desktop, etc)
    '/lib': Shared Libraries
    '/opt': Third party applications
    '/sbin': System Administration binaries
    '/tmp': Temporary Files
    '/usr': User data and programs
    '/var': Data Logs
```
A `path` is a sequence of directories that shows where a file or directory is located: "/home/Desktop" shows the full path of the Desktop location.

Common Commands
|Commands | Description              |Example            |
|:-------:|:-------------------------|:------------------:|
|`echo` | Returns or displays given texts/strings|`echo "What's up?"` |
|`pwd` | Print Working Directory: Displays the full path of the current directory | `pwd` |
|`cd` | Change Directory can take absolute paths, the name of the directory within a current directory, or shortcuts:<ul> <li>__.__: Current directory</li> <li>__..__: moves up a level</li> <li> __~__: home directory</li> <li>__-__: previous directory</li></ul>| `cd /python/fastapi/project` |
|`ls` | List Directories will display the contents of the current directory or of another if a _path_ is provided. It can utilise additional flags: <ul> <li>__-a__: The all flag allows hidden files (files that start with _._) to be viewed </li> <li>__-l__: The long flag will returned detailed information.</li> <li>__-r__: Reverses the returned directories.</li> </ul> | `ls -al` |
|`touch` |

## Text Processing
## The Kernel

## Process Management
A __program__ is a passive entity such as a file on a disk, while a __process__ is an active entity which is a program that is currently running or executing with its own space, memory, and stack. This helps the OS distinguish between the code and the execution so that is can safely run multiple instances of the same program. The _kernel_ manages them and each is assigned a sequential `proccess ID (PID)`. _Example: Typing `ls` in the terminal loads the `/bin/ls` program from the disk which becomes a process with a unique PID. The `ps` command shows the processes running in a specific terminal_:
```plaintext
$ ps

PID        TTY     STAT   TIME          CMD
41230    pts/4    Ss        00:00:00     bash
51224    pts/4    R+        00:00:00     ps
```
- __PID__: Process ID
- __TTY__: The _teletypewriter_ is the controlling terminal for the process
- __STAT__: Current status
- __TIME__: The amount of CPU time the process used
- __CMD__: The command that started the process

The _ps_ command can be used with other styles like BSD which does not use any dashes for the command, such as adding `aux` which gives more information like the user, memory etc.,:
- _a_: All process for all users
- _u_: User-oriented format
- _x_: Shows processes not attached to any terminal

The _ps_ command is often used with the __System V__ style to give a detailed view of all processes, which is often the first step in diagnosing an issue:
```bash
ps -ef
```
- _e_: Selects every process on the system
- _f_: Full-format listing

The `top` command shows which processes are using the most CPU or memory and refreshes every few seconds.

The __Controlling Terminal__ is the specific device (real or virtual) that manages the input (keyboard) and output (screen) for a session. __Terminal devices (TTY)__ were historically physical teletype machines, however, in modern Linux devices there are represented as files in `/dev/tty` directory. Modern graphical windows or remote connections (SSH) used __Psuedo devices (PTS)__. These are the terminals that are most commonly used, where the command `ps tty` shows which pseudo terminal is in use. Processes are bound to a controlling terminal where it can be terminated if the terminal closes. Processes like daemons which run in the background and manages system's services are not tied to a controlling terminal because they should not be accidentally terminated.

In Linux systems processes aren't created from scratch instead they are clones of a parent process. It is created through the `fork()` function. _Example_: When the `ls` command is used the shell calls `fork()`. After forking the child program can continue using the same program as the parent or call `execve` to have it's own program, which loads a new process and memory space when executed. Once it's finished it disappears and leaves the shell waiting for a new command. This is the __fork-exec__ model for creating a process. That means that every process is a child of another, however, there is a root process called __init__. This is the process that loads when the system boots with a PID of 1.

Processes __terminate__ through the `_exit` system call where they return a status code of __0__ (success) or any other number (error). For a proper cleanup, a parent runs a `wait` command. However, if the parent terminates before the child, then the child is an __orphan__ process and the _init_ process will act as the parent. A __zombie__ process is one that is waiting for the parent to complete the exit cleanup.

__Signals__ are software interrupts that informs a proces of an event that occurred, serving inter-process communication (IPC). They allow the OS or user to tell the process to stop, cleanup, restart etc. Common Signals include:
|Signal     | Shortcut/Command          | Description         |
|:---------:|:-----------------:|:--------------------|
|__SIGCONT__| kill -CONT <PID> | Resumes a process after it was stopped |
|__SIGINT (2)__ | `CTRL + C` | Interupt that informs the process to stop |
|__SIGHUP (1)__ |`kill -HUP <PID>`|Hang-up that tells the process to reload its configuration files |
|__SIGKILL (9)__ | `kill -9 <PID>` | Kill is immediate forceable stop of a process |
|__SIGSEGV (11)__| - | Segment Violation that is automatically run by the OS when a process attempts to access memory it has no authorisation to |
|__SIGSTOP/SIGSTP__ | `CTRL + Z` | Pauses a process. _SIGSTOP_ cannot be ignored |
|__SIGTERM (15)__| `kill` | Graceful termination |

Processes can take several actions against signals:
1. Ignore: Process continues what is it doing.
2. Catch: Responds to the signal.
3. Default action: May just terminate a process.
4. Block: May place the signal in a signal mask that only performs the requested action if unblocked unless the signal sent is a forcible one.

Processes are given different CPU time which can be adjusted with the `nice` and `renice` values, where _nice_ is used to start a process with a specific level and _renice_ is used to change the priority level of an already running process. The priority levels ranges from __-20 (high-priority, low niceness)__ to __19 (low-priority, high niceness)__.
```bash
# View niceness look for NI column
top

nice -n 10 find / -name "look.txt"

renice 6 -p 1234
```

As a CPU can only do one thing at a time, __Process States__ are used to keep track of the current lifecycle of a process. The most common state codes include:
| State Code | Description      |
|:----------:|:-----------------|
|`D` | Uninterruptible Sleep, cannot be bothered and may indicate something is wrong with hardware is in this state for too long |
|`R` | Ruuning or Runnable | Process is actively running or waiting to be given CPU time to run |
|`S`| Interruptible Sleep. The process is waiting for something to occur like touching a key. Most processes live here |
|`T` | Stopped. Process was paused or stopped and can be resumed with SIGCONT |
|`Z` | Zombie. Completed and waiting for the parent to terminate it from the process table |

Everything is Linux is based on files. The `/proc` virtual filesystem is used to store information about running process. It isn't stored on the hard drive but instead is created in memory and gives details of the internal data sturctures and state of a system.
```bash
# View its contents
ls /proc

# View status of a specific process
cat /proc/1234/status
```

__Job Control__ is a feature of the shell that can be used to move processes to the foreground and the background.
```bash 
# Lock the terminal
sleep 100
# Use & to start in the background
sleep 100 &
sleep 200 &

# View jobs running in the background
jobs

# Stop a process and send it to the background CTRL + Z
bg
# Bring to foreground
fg # Most recent (+)
fg %<PID> # specific

# Kill a process
kill %<PID>
```
## Process Utilisation
The `top` command gives a real-time overview of the system's processes and utilisation. The first few lines provides a summary of the system's health:
- 1st line: Displays information like number of users, load average, current time, and system uptime for a period of 1, 5, and 15 minutes. This information can also be obtained by using `uptime`.
- 2nd line: summary of all processes categorised as zombie, running, sleeping, or stopped
- 3rd line: Provides information about the CPU utilisation:
     - _user_: percentage of time spent running user apps (processes) that are not nice
     - _sys_: percentage of time spent running the kernel and its processes
     - _idle_: percentage of time the CPU is doing nothing
     - _ni_: percentage of time running nice (low priority) apps
     - _wa_: Percentage of time spent waiting for I/O processes where a high percent may indicate a network or hardware issues
     - _hi_: percentage of time spent servicing harware interrupts
     - _si_: percentage of time spent servicing software interrupts
     - _st_: steal time that are showed in virtual environments showing the time the virtual CPU waits for the real CPU.
- 4th/5th line: Shows a memory swap where if the RAM is full, storage is moved to the disk (swap)

The main body provides a detailed process list sorted by who is using the most CPU by default. it includes information (headers) like _USER, PID, STATE, COMMAND, %CPU, %MEM, TIME, PR (scheduling priority), SHR (Shared memory), RES (non-swapped physical memory), VIRT (total memory process can access), and NI_.
```bash
# Monitor a specific process
top -p 1234
```

The __list open files__ `lsof` command provides infomration about files and which processes are using them. It is the tool that informs you that a USB is in use if you try to eject it.
```bash
# See the processes using the current directory
lsof .

# Process using internet connect on a port
lsof -i :8080

lsof -p 1234 # Use top to find a specific PID
```

The __file user__ `fuser` command is used for terminating processes on a specific file or mount point usinf the `-k` flag which calls _SIGKILL_.
```bash
sudo fuser -k /mnt/usb
# View processes accessing a particular resource with verbose
fuser -v .
# Unmount a disk
fuser -m /mnt/data
```

__Threads__ are small units of execution within a process that share the process's memory. Threads are important because they are fast and cheap to create and allows multiple things to get done simultaneously, as opposed to cloning a new process which is expensive for the CPU.
🧠 __Mental Model__: The process is the entire kitchen space and all it's appliances while threads are individuals users and chefs of the space and resources.

Processes can be __single threaded__ or __multi-threaded__. For example, one thread can be managing input while another is type checking. Threads can be __concurent__ or __parallel__, where concurrency is about managing tasks where processes may start at the same time but finish at different times over a time period, while parallelism is where process execute and finish at the same time. Concurrency is good for responsiveness in tasks libe web-servers, real-time systems, and GUIs by improving resource efficiency. Parallelism is good for ML, scientific simulations, and video rendering, as it breaks task into sub-tasks for completion.
- _Example_: 
    - __Concurrency__: Listening to music on a laptop with one core and programming in an IDE. The OS switches between the tasks where it appears simultaneous but only one thing is happening at a time
    - __Parrellism__: There are multiple cores where one is dedicated to video rendering and another for typing.

Threads can be shown by using the `ps` command with the `-m` or `-M` (Mac) flag.
```bash
# Specific Process
ps -mp 1234
```
It shows the main thread and its sub-threads.

The `uptime` command informs the user of how long the system has been running, how many users are logged in, and the system load. The __load average__ shows three numbers which are the average number of processes in the in CPU queue either running or waiting to run over a period of 1, 5, or 15 minutes. This information is highly important for process utilisation and system performance. If the load average is 1.0 and a computer has 4 CPU cores then it's 25% of the CPU capacity being used.
```bash
# View number of cores
cat /proc/cpuinfo
# For Mac
sysctl -n machdep.cpu.core_count
```
_For good system performance, keep the load average below the number of cores_.

The `iostat` command provides the amount of time the CPU is waiting for disks to respond. The output has two main sections:
|CPU Metrics/Utilisation        |Disk Utilisation          |
|:-----------------------------|:--------------------------|
|Provides information of the the processor is using its time:<ul> <li>__%user__: time spent executing user level apps</li> <li>__%nice__: time spent on user level _nice_ apps</li> <li>__%sys__: time spent executing system level processes</li> <li>__%iowait__: time the CPU was idle waiting for a disk I/O request to complete, where high times indicates a bottleneck </li> <li>__%steal__: the time the virtual CPU is waiting for the _hypervisor_ to finish servicing another processor </li> <li>__%idle__: time the CPU was idle and not waiting for any I/O disk request</li> </ul>| Focuses on __I/O__ monitoring by displaying how data is being moved to and from storage devices:<ul> <li>__tps__: Transfer per second, the amount of I/O requests</li> <li>**kB_read/s**: the amount of data read from the device in kilobytes per second</li> <li>**kB_wrtn/s**: The amount of data being written to the device in kilobytes per second</li> <li>**kB_read**: total number of data read from the device since last reboot</li> <li>**kB_wrtn**: total number of data written to the device since the last reboot</li> </ul>|

The memory of a system greatly affects its performance when it's low. The __virtual memory statistics__ `vmstat` command is a memory utilisation tool that provides information about processes, memory, paging, block I/O, traps, and CPU activity. If used without arguments it returns information about the memory since the system's last boot.
```bash
vmstat
# Mac
vm_stat
```
- __Procs__: _r_: number of runnable processes waiting for runtime, _b_: number of uninterruptible sleep
- __Memory__: _swpd_: number of virtual memory used, _free_: number of idle memory, _buff_: amount of memory used as buffers, _cache_: number of memory used as page cache
- __Swap__: _si_: amount of memory swapped in from disk per second, with high values indicating the system is low on memory, _so_: amount of memory swapped out to disk per second
- __IO__: _bi_: blocks received from a block device, _bo_: blocks sent to a block device
- __System__: _in_: number of interrupts per second, _cs_: number of context switches per second
- __CPU__: shows the percentage of CPU time

To continuous monitor a system the __sar__ tool can be used:
```bash
sudo apt install sysstat

# Information from the start of the day
sudo sar -q
sudo sar -r # memory usage from the start of the day
sudo sar -P # CPU usage

# View a different day
sar -q /var/log/sysstat/sa<day> # in number format
```
The __cron__ daemon is a background process used for automation to help with scheduled tasks called __cron jobs__. The __crontab__ command is used to interact with cron jobs, where the 5 time slots are the schedule and can be replaced with the _*_ wilcard indicating to always run:
```plaintext
* * * * * <command/job>
```
Time slots:
1. Minute (0-59)
2. Hour (0-23)
3. Day of Month (1-31)
4. Month (1-12)
5. Day of Week (0-6, where 0 is Sunday)

```bash
crontab -e # Edit jobs
cron -l # list jobs
```

## Filesystem
The __Linux Filesystem Hierarchy (FHS)__ standard ensures that Linux systems store files in a predictable and consistent manner for OS developers and users alike.
```bash
# Linux: View top-level directories
ls -l /
``` 
The `/` __Root__ directory is the starting point for every directory on the system and is used in every _absolute path_. It's the parent directory with branches that have leaves (the actual files users work with).
|Directory Type | Description  |  Directories  |
|:-----------|:-------------|:-----------|
|__System Directories__| Contains information about the system's operation| <ul> <li>__Binaries__ `/bin`: stores important executable __comand-line__ commands like _ls_ </li> <li>__System Binaries__ `/sbin`: Contains system binaries that administrators and __root__ users can utilise</li> <li>__Et Cetera__ `/etc`: Core system configuration folder with files for configuring the OS and installed applications. Should not contain executable files</li> <li>__Library__ `/lib`: Stores sharable library files the _bin_ and _sbin_ rely on</li> <li>`/boot`: Stores the system boot processing files</li></ul>|
|__User and Application Data__| Holds the information about the specific user and the applications they installed | <ul> <li>__Home__ `/home`: Personal directories like desktop and documents</li> <li>__Root Home__ `/root`: Private home directory for the administrator</li> <li>__Optional__ `/opt`: For installing optional and third-party software</li> <li>__User System Resource__ `/usr`: Contains user-installed software and utilities with sub-directories like `/usr/bin` and `/usr/local`</li> </ul> |
|__Dynamic and Temporary Data__| Stores changeable files | <ul> <li>__Variables__ `/var`: Files that are expected to change and stores things like system logs</li> <li>__Temporary__ `/tmp`: Files here are generally deleted on system reboot</li> <li>__Run-time variable data__ `/run`: Stores volatile runtime data like process ids and unix sockets since the last boot</li> </ul>|
|__Device and Mount Point__| Hardware information| <ul> <li>__Devices__ `/dev`: Holds files that represent hardware components like input devices</li> <li>`/media`: SD cards USB drives, and CD-Rom</li> <li>__Mount__ `/mnt`: Used as a temporary mount point for filesystems and storage</li> </ul> |
|__System Information__| - | <ul> <li>__Processes__ `proc`: Virutal files that contains information about the processes and the kernel</li> <li>__Server__ `/srv`: File specific data served by the system, like files for web servers</li> </ul> |

There are many different filesystem types like _FAT32_ that needs to be organised in a standard way to make them easy for the user. This is done by using the __Virtual Filesystem__ (VHS), a software layer in the kernel that operates like a universal translator. A __Journaling Filesystem__ keeps a log of what it is about to do before it changes data on a disk. This helps the system continue working on tasks by picking where it left in cases where a crash or interruption may occur. It highlights errors in a log file where the command `fsck` may not be needed to scan errors on a disk. The `df` command shows the filesystem type on a Linux distro and there are several:
- __Fourth Extended (EXT4)__: The standard Linux OS fast stable and has built-in journaling
- __eXtended Filesystem (XFS)__: Great for massive files like databases and video editing services as it has built-in journaling
- __B-tree FS (Btrfs)__: Snapshots. It can "take a picture" of your disk and let you "roll back" the entire OS if a change breaks it.
- __Apple Filesystem (APFS/HFS+)__: Optimized for SSDs and has a feature called "Copy-on-Write" which makes copying files instant.
- __Windows Filesystem (NTFS and FAT)__: Linux provide support for reading and writing to them

Hard disks are subdivided into __partitions__ such as _/dev/sda_ being the full disk and _/dev/sda1_ being one of the partitions or blocks which the OS uses to coordinate the system. Partitioning provides isolation and allows one to use multi OS on a single system. A __partition table__ informs the OS how the disk is partitioned and there are two main types:
1. __Master Boot Record (MBR)__: The traditional model that has a limit of 2TB and 4 primary partitions
2. __GUID Partition Table (GPT)__: The modern standard for disk partitioning where each partition is assigned a _Globally Unique Identifier (GUID)_

The core of a filesystem structure is a database used to manage files, where the structure consists of:
- __Boot Block__: One per OS used to boot the OS and not used by the filesystem itself
- __Superblock__: a single Sector that follows the boot block containing metadata about the filesystem
- __Inode table__: The database that manages the files and directories, wehre each file and directory has a unique entry.
- __Data blocks__: where the actual content of the files and directories are stored

There are several tools that can be used to partition a disk:
|Tools     |Description   |Example Use     |
|:---------|:-------------|:--------------:|
|__fdisk__| Basic command line partition tool that does not support GPT|`sudo fdisk /dev/sdb` |
|__parted__ |A powerful command-line tool used in automation and scripts| List existing partitions: `sudo parted -l` |
|__gparted__ | Graphical version of _parted_|-|
|__gdisk__| Only supports GPT| -|

To launch the environment use the `sudo parted` tool and run commands to manage partitions:
```bash
# Select the specific disk to modify
select /dev/sdb

# View the partition table
print

# Create a partition: Specify the start and end points
mkpart primary ext4 1MB 500MB

# Resize existing partition using a number and specifying its endpoint
resizepart 2 700MB
```
The command `diskutil list` lists the partitions on a Mac.

The make filesystem `mkfs` command is used to create a variety of different partitions:
```bash
sudo mkfs -t ext4 /dev/sdb2 # Specific partition
```
```zsh
diskutil eraseVolume APFS "MyNewDrive" /dev/disk2s1
```

__Mounting__ is the process of attaching a formatted filesystem to a specific directory in your existing file tree. That directory is called the __Mount Point__.
```bash
# Create mount point
sudo mkdir /somefolder
# Attach the device
sudo mount -t ext4 /dev/sdb2 /somefolder
# Attach using UUID
sudo blkid # View UUIDs for blocks
sudo mount UUID =130b882f-7d79-436d-a096-1e594c92bb76 /somefolder

# Unmount by mount point
sudo unmount /somefolder
sudo unmount /dev/sdb # Unmount by device name
```
The `swap` command is used to allocate virtual memory for mounts and should be twice as much space as needed, though modern systems generally have enough space in their RAM.
```bash
mkswap /dev/sdb2 # Initial swap area
swapon /dev/sdb2 # Enable device
swapoff /deb/sdb2 # Remove swap
```
The __Filesystem Table__ located in `/etc/fstab` contains a permanent file with the filesystems that the system should mount on start-up. Each row in the /etc/fstab file contain a filesystem that follows a strict six column format:
|Column       |Description        |Example            |
|:------------|:------------------|:-----------------:|
|__Device Identifier__| Specifies the device to mount using a unique identier| `UUID=550e8844` |
|__Mount Point__| The directory in the filesystem where the device would be mounted |`/mnt/data` |
|__Filesystem Type__| The type of filesystem on the device| `ext4`, `xfs`, `btrfs`, `swap` |
|__Options__| Rules for how the filesystem is mounted| `defaults`, `relatime`, `errors=remount-ro` |
|__Dump__| Used by the `dump` utility if a filesystem needs to be backed up| `0`: Default, no backup |
|__Pass__| Used by `fsck` to determine the order of the filesystems at boot time | `1`: Root should be 1, `0`: filesystem would not be checked|

There are two important commands used to manage disk space on a Linux system:
1. Disk Free (__df__): High-level view of the storage for mounted systems - `df -h`. The command `df -i` gives information about the inode space used
2. Disk Usage (__du__): Specified storage information for files and directories - `du -h /home/fish`

The `fsck` command is used to check the filesystem and may try to repair it:
```bash
sudo fsck /dev/sdb
```

An __inode__ (index node) is an entry in the inode table that contain most information about a file or directory except for its name and actual content. When a filesystem is created aalgorithms automatically create space for it. However if the inode table is full no new files or directories can be created as data storage depends on both the data blocks and the _database_ (inode table). Each inode is given a unique sequenial number that can be reused when old inodes are deleted.
```bash
# View remaining inodes
df -i
# View inode numbers
ls -li

# View specific file
ls -i filename.txt
stat filename.txt
```
Each inode points to the data block for files.

__Symbolic Links__ (Symlink) are soft links used to create a shortcut to other files or directories. __Hard Links__ is a second name of a file that points to the same inode; similar to someone having two names.
```bash
ln -s /path/to/original /path/to/link # Soft link creation
ln /path/to/original /path/to/link # Hard link creation
```

## Permissions



## User Management


## Resources
- [LabEx](https://labex.io/linuxjourney)
- [Linux Foundation](https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html)


Switch back to zsh: chsh -s /bin/zsh