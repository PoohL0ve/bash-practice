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
## Process Utilisation
## User Management


## Resources
- [LabEx](https://labex.io/linuxjourney)
