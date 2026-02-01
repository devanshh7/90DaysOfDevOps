# Linux Troubleshooting Drill: CPU, Memory, and Logs
In this log , I will perform some commands related to disk, memory, troubleshooting or logs commands and will print the iutput of those command also along with the commands.
## Environment basics
- `uname -a` : Shows kernel version, architecture, and OS type — confirms what kernel you’re actually running.
#### Output
```
Linux DESKTOP-J561SE4 6.6.87.2-microsoft-standard-WSL2 #1 SMP PREEMPT_DYNAMIC Thu Jun  5 18:30:46 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
```

- `lsb_release -a / cat /etc/os-release `: Displays Linux distribution and version — crucial for knowing package managers, paths, and known bugs.
#### Output
```
~$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.2 LTS
Release:        24.04
Codename:       noble
______________________________________________
devansh@DESKTOP-J561SE4:~$ cat /etc/os-release
PRETTY_NAME="Ubuntu 24.04.2 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.2 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo
____________________________________________________
```
Screenshot of output
<img width="809" height="479" alt="image" src="https://github.com/user-attachments/assets/07f656ce-cce2-42e0-b952-0f166e2a9192" />

## Filesystem sanity
- `mkdir /tmp/runbook-demo` : Creates a test directory — verifies filesystem is writable and permissions are same.
#### Output
```
~$ mkdir /tmp/runbook-demo
_______________________________________________
devansh@DESKTOP-J561SE4:~$ ls /tmp/

hsperfdata_jenkins
hsperfdata_jenkins-agent
jetty-0_0_0_0-8080-war-_-any-13755678805147559725
runbook-demo
snap-private-tmp
systemd-private-a8d2282e5196465d917ff6ecaa3bd28f-polkit.service-yCW0Jv
systemd-private-a8d2282e5196465d917ff6ecaa3bd28f-systemd-logind.service-u1XH6N
systemd-private-a8d2282e5196465d917ff6ecaa3bd28f-systemd-resolved.service-xU8RM9
systemd-private-a8d2282e5196465d917ff6ecaa3bd28f-systemd-timesyncd.service-hYegTz
systemd-private-a8d2282e5196465d917ff6ecaa3bd28f-wsl-pro.service-PP3xPz
winstone986195995673797479.jar
```
- `cp /etc/hosts /tmp/runbook-demo/hosts-copy`: Tests file read/write and copy operations — catches permission or disk issues early.
Creates directory with name "hosts" and copy to path `/etc/hosts` and than copy and update its name to "hosts-copy" to location `/tmp/rubook-demo/hosts-copy`
#### Output
```
devansh@DESKTOP-J561SE4:~$ cp /etc/hosts /tmp/runbook-demo/hosts-cpoy
---------------------------------------------------------------------
devansh@DESKTOP-J561SE4:~$ ls /tmp/runbook-demo/
hosts-cpoy
```

- `ls -l /tmp/runbook-demo`: Shows ownership, permissions, and file sizes — confirms files were created correctly.
#### Output
```
~$ ls -l /tmp/runbook-demo
total 4
-rw-r--r-- 1 devansh devansh 1351 Feb  1 16:03 hosts-cpoy
```
<img width="796" height="399" alt="image" src="https://github.com/user-attachments/assets/645e6f1f-ced0-48f9-b81a-a16e3de8fcbd" />

## CPU / Memory
- `top / htop` : Live view of CPU, memory, and top processes — quickly spots spikes, runaway processes, or OOM risk.
#### Output
```
~$ top
top - 16:06:54 up  6:31,  2 users,  load average: 0.00, 0.01, 0.04
Tasks:  53 total,   1 running,  52 sleeping,   0 stopped,   0 zombie
%Cpu(s):  1.2 us,  0.4 sy,  0.0 ni, 98.3 id,  0.0 wa,  0.0 hi,  0.1 si,  0.0 st
MiB Mem :   1836.2 total,     38.7 free,   1320.0 used,    561.8 buff/cache
MiB Swap:   1024.0 total,    987.2 free,     36.8 used.    516.2 avail Mem
```

- `ps -eo pid,pcpu,pmem,comm -p <pid>`: Shows CPU and memory usage for a specific process — isolates resource hogs.
#### Output
```
~$ ps -eo pid,pcpu,pmem,comm --sort=-pcpu | head
    PID %CPU %MEM COMMAND
  20180  100  0.2 ps
  20179 50.0  0.4 mysqladmin
   1609  2.0 25.1 java
   1285  1.5 18.4 mysqld
   1938  0.8  4.9 java
  19892  0.3  0.2 htop
    770  0.2  2.1 dockerd
    543  0.1  1.5 containerd
      1  0.0  0.6 systemd

```

- `free -h`: Displays total, used, and available memory — checks for memory pressure or swap usage.
#### Output
```
~$ free -h
               total        used        free      shared  buff/cache   available
Mem:           1.8Gi       1.3Gi        51Mi       3.6Mi       557Mi       524Mi
Swap:          1.0Gi        45Mi       978Mi
```
<img width="739" height="336" alt="image" src="https://github.com/user-attachments/assets/24f17dd3-99ce-40b5-a830-faa36cbd43e6" />


## Disk / IO
- `df -h`: Shows filesystem disk usage — detects full disks that can crash services.
#### Output
```
~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
none            919M     0  919M   0% /usr/lib/modules/6.6.87.2-microsoft-standard-WSL2
none            919M  4.0K  919M   1% /mnt/wsl
drivers         238G  100G  138G  42% /usr/lib/wsl/drivers
/dev/sdd       1007G  6.1G  950G   1% /
```

- `du -sh /var/log`: Reports total size of logs — identifies log growth causing disk exhaustion.
#### Output
```
~$ du -sh /var/log
du: cannot read directory '/var/log/private': Permission denied
387M    /var/log
```
- `iostat / vmstat / dstat`:Displays disk and CPU IO activity — finds IO bottlenecks or saturation.
#### Output
```
$ iostat
Command 'iostat' not found, but can be installed with:
sudo apt install sysstat
devansh@DESKTOP-J561SE4:~$ sudo apt install sysstat
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
----------------------------------------------------
~$ iostat
Linux 6.6.87.2-microsoft-standard-WSL2 (DESKTOP-J561SE4)        02/01/26        _x86_64_        (4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.20    0.01    0.69    0.72    0.00   97.38

Device             tps    kB_read/s    kB_wrtn/s    kB_dscd/s    kB_read    kB_wrtn    kB_dscd
sda               0.05         3.27         0.00         0.00      78977          0          0
-------------------------------------------------------------
~$ vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 1  0  90644 175244  32192 453632    0    3    55    29  398    1  1  1 97  1  0  0
-----------------------------------------------------------------------

```
<img width="802" height="424" alt="image" src="https://github.com/user-attachments/assets/fd20cca1-f20e-486e-bbc6-65b42dcddc61" />  <img width="940" height="339" alt="image" src="https://github.com/user-attachments/assets/3dddef09-fab8-4709-ad15-4f2f44197e11" />


## Network
- `ss -tulpn / netstat -tulpn`: Lists listening ports and owning processes — confirms service is bound and reachable.
#### Output
```
~$ ss -tulpn
Netid      State       Recv-Q      Send-Q           Local Address:Port              Peer Address:Port      Process
udp        UNCONN      0           0                   127.0.0.54:53                     0.0.0.0:*                0.0.0.0:*
udp        UNCONN      0           0                        [::1]:323                       [::]:*
tcp        LISTEN      0           4096
----------------------------------------------------------------------
~$ netstat -tulpn
(No info could be read for "-p": geteuid()=1000 but you should be root.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -
```
- `curl -I <service-endpoint>/ping`: Checks HTTP connectivity and response headers — verifies service is up without full payload.
#### Output
```
~$ ping google.com
PING google.com (142.250.183.206) 56(84) bytes of data.
64 bytes from bom07s33-in-f14.1e100.net (142.250.183.206): icmp_seq=1 ttl=110 time=17.9 ms
64 bytes from bom07s33-in-f14.1e100.net (142.250.183.206): icmp_seq=2 ttl=110 time=7.57 ms
64 bytes from bom07s33-in-f14.1e100.net (142.250.183.206): icmp_seq=3 ttl=110 time=8.67 ms
64 bytes from bom07s33-in-f14.1e100.net (142.250.183.206): icmp_seq=4 ttl=110 time=10.1 ms
64 bytes from bom07s33-in-f14.1e100.net (142.250.183.206): icmp_seq=5 ttl=110 time=8.89 ms
64 bytes from bom07s33-in-f14.1e100.net (142.250.183.206): icmp_seq=6 ttl=110 time=12.4 ms
^C
--- google.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5008ms
rtt min/avg/max/mdev = 7.571/10.921/17.886/3.461 ms
----------------------------------------------------------------------
~$ curl -I google.com
HTTP/1.1 301 Moved Permanently
Location: http://www.google.com/
Content-Type: text/html; charset=UTF-8
Content-Security-Policy-Report-Only: object-src 'none';base-uri 'self';script-src 'nonce-N5YWZs_6WlmHthfBKPUBGQ' 'strict-dynamic' 'report-sample' 'unsafe-eval' 'unsafe-inline' https: http:;report-uri https://csp.withgoogle.com/csp/gws/other-hp
Date: Sun, 01 Feb 2026 16:23:18 GMT
Expires: Tue, 03 Mar 2026 16:23:18 GMT
Cache-Control: public, max-age=2592000
Server: gws
Content-Length: 219
X-XSS-Protection: 0
X-Frame-Options: SAMEORIGIN
```
<img width="1056" height="522" alt="image" src="https://github.com/user-attachments/assets/d8a6e25d-261c-434d-9dc9-b3dce7de2651" />
<img width="971" height="528" alt="image" src="https://github.com/user-attachments/assets/0bc52d02-1b56-465c-9b92-277fc48054d6" />


## Logs
- `journalctl -u <service> -n 50`: Shows recent systemd logs for a service — surfaces crashes, restarts, and errors. 
#### Output
```
~$ journalctl -u jenkins -n 10
Feb 01 04:56:19 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:19.505+0000 [id=30]        INFO        jenkins.InitReac>
Feb 01 04:56:19 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:19.525+0000 [id=36]        INFO        jenkins.InitReac>
Feb 01 04:56:19 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:19.698+0000 [id=56]        INFO        hudson.util.Retr>
Feb 01 04:56:20 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:20.137+0000 [id=34]        INFO        jenkins.InitReac>
Feb 01 04:56:20 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:20.712+0000 [id=24]        INFO        hudson.lifecycle>
Feb 01 04:56:20 DESKTOP-J561SE4 systemd[1]: Started jenkins.service - Jenkins Continuous Integration Server.
Feb 01 04:56:44 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:44.431+0000 [id=56]        INFO        h.m.DownloadServ>
Feb 01 04:56:47 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:47.078+0000 [id=56]        INFO        h.m.DownloadServ>
Feb 01 04:56:49 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:49.697+0000 [id=56]        INFO        h.m.DownloadServ>
Feb 01 04:56:49 DESKTOP-J561SE4 jenkins[1609]: 2026-02-01 04:56:49.699+0000 [id=56]        INFO        hudson.util.Retr>
```
- `tail -n 50 /var/log/<file>.log`: Reads last log entries — quickly checks for fresh errors or warnings.
#### Output
```
~$ tail -n 20 /var/log/dpkg.log
2026-02-01 04:58:21 configure traceroute:amd64 1:2.1.5-1 <none>
2026-02-01 04:58:21 status unpacked traceroute:amd64 1:2.1.5-1
2026-02-01 04:58:22 status half-configured traceroute:amd64 1:2.1.5-1
2026-02-01 04:58:22 status installed traceroute:amd64 1:2.1.5-1
2026-02-01 04:58:23 trigproc man-db:amd64 2.12.0-4build2 <none>
2026-02-01 04:58:23 status half-configured man-db:amd64 2.12.0-4build2
2026-02-01 04:58:28 status installed man-db:amd64 2.12.0-4build2
2026-02-01 16:17:17 startup archives unpack
```
<img width="995" height="266" alt="image" src="https://github.com/user-attachments/assets/ae7ab5db-bdbd-47f7-a809-52eac20e91db" />
<img width="1001" height="639" alt="image" src="https://github.com/user-attachments/assets/e357524a-33fd-45e3-a68f-b27b283253d6" />
