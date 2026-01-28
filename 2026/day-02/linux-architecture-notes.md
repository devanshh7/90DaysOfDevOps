# This is my task 2 notes
- What Linux is: It is an operating system(it has many versions like Ubuntu, Debian, CentOS etc.) just like there are others i.e Windows, MacOS etc.
- It is an operating system that is developed by Linus Torvals in 1991 , linux came from unix family and it is open source software OS .
- In linux everything is represented as directories or a file, files are stored in directory and every directory contains a file with a tree structure i.e. "File System Hierarchy"
- The hierarchy start with / (forward slash)- which is ROOT Directory-> top level directory in linux.
- It is imp because almost 90% of the apps are built on linux.
- Linux server are known for their igh stability and security.

# Process and Different process states
- Process : a process is an instance of a running program that goes through several distinct states during its lifecycle. The kernel manages these states to efficiently allocate resources like the CPU and memory.

'D' = UNINTERRUPTABLE_SLEEP

'R' = RUNNING & RUNNABLE

'S' = INTERRRUPTABLE_SLEEP

'T' = STOPPED

'Z' = ZOMBIE
  <img width="741" height="831" alt="image" src="https://github.com/user-attachments/assets/89fb7929-c095-415f-8308-e8382e495ab0" />

- Running process(R): displays the activeky running instruction on the CPU or is ready for run or waiting in queue.
Whenever the process is created it is in running state.

- Stopped(T): The process has been suspended, usually signal SIGSTOP or SIGTSTP, which is sent by pressing Ctrl+Z in a terminal. A stopped process can be resumed by sending it a SIGCONT signal.
- Zombie (Z): A process will terminate when it calls ‘system exit’ API or when someone else kills the process.
  When a process terminates, it will release all the data structures and the resources it holds. However, it will not release it’s slot in the ‘process’ table. Instead, the process will send a SIGCHLD signal to its parent process. Now it’s up to the parent process to release the child process slot in the ‘process’ table.
--Sleeing: SLEEPING state indicates the process is currently waiting on certain resources (like waiting on I/O, waiting on locks, application code making the process to sleep,…). There are two types of SLEEPING processes,
- INTERRUPTABLE_SLEEP (D): When a process is in INTERRUPTABLE_SLEEP, it will wake up from the middle of sleep and will process new signals sent to it.
- UNINTERRUPTABLE_SLEEP(S): When a process is in UNINTERRUPTABLE_SLEEP, it will not wake up from the middle of sleep even though new signals are sent to it.

# 5 Commands you will use daily
- mkdir : it is a command used to make a folder in a directory.
  Usage: mkdir directory_name
Example Option: mkdir -p /path/to/new/nested/directory creates all parent directories in the path if they don't already exist.

- ls (list): This command lists the files and directories within your current working directory.
Usage: ls
Example Options: ls -l for a detailed (long) view,
ls -a to show hidden files (those starting with a dot), or
ls -lh for human-readable file sizes.

- cd (change directory): Used to navigate between directories in the file system.
Usage: cd /path/to/directory
Quick Navigation: cd ~ goes directly to your home directory;
cd .. moves one directory up; cd - switches back to your previous directory.

- pwd (print working directory): This command tells you the full path of your current location in the directory structure.
Usage: pwd
Example Output: /home/username/documents

- cat (concatenate): Primarily used to display the contents of a text file directly in the terminal.
Usage: cat filename.txt
Other Uses: It can also be used to combine multiple files into one, using redirection.


# The core components of Linux (kernel, user space, init/systemd)
- Linux is an operating syatem -> which have heart(core of linux) called Kernel. -> Linux Kernel
  LINUX Architecture works on principle A-S-K
  1. Application- are the prog or s/w used by users i.e whatsapp, fb, docker, git etc.
  2. Shell- Then the next layer is shell, it is the interactive way to talk with kernel.
  We can talk to kernel using " Shell Commands "
  3. Kernel- It is a computer program that understands by the machine (i.e binary 0101) 
  4. Hardware- h/w like CPU, RAM , Printers etc.
     
# How processes are created and managed
Every time you launch an application or execute a command, the system creates a process for it. It involves controlling and monitoring all the running programs on the system.
"A process means a program in execution. It generally takes an input, processes it, and gives us the appropriate output"
1. ps : show process status. Often used with grep e.g.
2. ps -ef: print detailed overview
3. top: displays sorted information about processes
4. htop: Display sorted information about processes with visual highlights. It allows you to scroll vertically and horizontally, so you can see every process running on your system and
entire commands
5. kill <pid> : Kill a process specified by its process ID PID,
which you obtain using the ps command

# What systemd does and why it matters
"Systemmd is the default and 1st process that runs when the system is boots on."
(PID 1) that boots the system, manages user-space processes, and controls services in parallel. It matters because it significantly faster startup times, offers consistent unit configuration, and streamlines administration via systemctl for logging, device management, and scheduling. 

Key Functions of Systemd
- PID 1 Process: As the first process, it initializes the system, mounts file systems, and starts all other daemons.
- Service Management: Starts, stops, and manages background services (systemctl).
- Parallelization: Launches services in parallel during boot, rather than sequentially, improving boot speed.
- Unit Configuration: Manages system resources (services, devices, mount points) using unit files.
- Logging: Uses journald to collect and manage system logs in a binary format.
- Device Management: Replaced udev for managing device hotplugging. 

Why Systemd Matters->
Commands like systemctl, journalctl, and systemd-analyze provide powerful, unified tools for debugging and monitoring.
