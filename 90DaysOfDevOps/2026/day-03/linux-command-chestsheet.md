# Cheatsheet of commands on:
## File system
- `pwd (present working directory)` : it prints the current location
- `ls :` lists the names of files and subfolders in current directory.
- `ls -l :` shows detils of each item displayed i.e. user permisiion,type and time/date when it was last modified
- `ls -a :` display hidden files/folders. May combined with ls -l to form ls -al.
- `cd :` it means change directory i.e. cd / > takes you to root directory
- `cd .. :` takes you to the previous directory or take you one step backward
- `cmp A B :` compare two files , no output if A and B are identical , Outputs character and line no otherwise
- `diff A B :` compare two files for differ Outputs the difference
- `mkdir:` make directory is used to create a new folder
- `touch :` is cmd used to create a new file
- `cp [source] [dest] && mv [S] [D]:`  used to copy file from source to dest and same for move
- `cp -r A B && mv -r A B :` if you want to move or copy folder then we have to use -r before S -> D
- `rm <X> :` it removes X permanently
- `rm -f X :` forcibly remove file X witout prompt confirmation
- `rm -r Y && rm -rf Y :` Recursively delete a directory Y and its contents & Forcibly remove directory Y and its contents recursively
- `rmdir Y :`  Remove a directory Y permanently, provided is empty.
- `cat X:` view content of that file
- `cat -b <file>:` displays line numbers along with the output.
- `wc <file> && w -l <F>:` wc counts the no of line and words where as wc -l counts the total no of lines

## Process management
- `ps :` shows the current active process status, often used with grep e.g. $ ps aux
- `ps -ef :`  prints the detailed overview
- `ps -eo pid,user,command :` Display only the columns pid, user and command in ps output
- `ps aux :` ps with a lot of detail
- `kill <pid> :` kill the process with pid, sends SIGTERM(15) signal asking to terminate proc gracefully
- `kill -9 <pid> :` sends SIGKILL(9) forcing the process to terminate immediately.
- `killall <p_name> :` kill all the instance of process with name = p-name
- `bg :` lists stopped/background jobs, resume stoped job in background
- `fg :` bring most recent job to foreground
- `fg n :` bring job n to forground
-  `top :` Display sorted information about processes
-  `htop :` Display sorted information about processes with visual highlights. It allows you to scroll vertically and horizontally, so you can see every process running on your system and entire commands.
- `pstree :` visualize the process hhierarchy in tree format, shoeing relation btw parent and child
- `pgrep <procc_name> :` finds the PID of the specific process by its name
- `pidof <P_name> :` shows the PID of running program
- `nohup <command>:` runs command that continues to run even after you logout or close terminal
- `ps aux | gro <name> :` search for process


## Network Troubleshooting

- `ip addr/ ip link:` displays IP add, interface status and MAC add to verify physical/logical connectivity
- `ip routr/ route -n:` displays routing table to verify the default gateway and nw routing paths
- `ping <host> :` pings the host with its hostid
- `dig domain:` gets DNS or dimain
- `dig -x addr:` reverse lookup host
- `whois domain:` get whois for the domain
- `ifconfig :` displays all network interface with IP add
- `netstat :`Print open sockets of network connections,routing tables, interface statistics,masquerade connections, and multicast
memberships.Pipe with the less command: e.g.,`netstat -a
- `netstat -a:` shows both listening and non listening sockets
- `hosst domain:` displays DNS info for domain
- `wget <link> :` download from location link
- `curl <link> :`displays hTML source of LINK'
- `netstat -nutlp:` Show listening TCP and UDP ports and corresponding programs
