# Useful DevOps One-Liners

| Task | Command | Description |
|-----|--------|-------------|
| Find files older than 7 days | `find /path -type f -mtime +7` | Lists files modified more than 7 days ago |
| Delete files older than 30 days | `find /path -type f -mtime +30 -delete` | Cleans old logs or backups |
| Find large files (>100MB) | `find / -type f -size +100M` | Useful for disk cleanup |
| Count lines in all log files | `wc -l *.log` | Shows line count per log file |
| Replace text in multiple files | `sed -i 's/old/new/g' *.conf` | Batch replace configuration values |
| Show top 10 largest files | `du -ah / | sort -rh | head -10` | Identify disk usage issues |
| Monitor disk usage | `df -h` | Human readable disk usage |
| Monitor memory usage | `free -h` | Shows RAM usage |
| Check running processes | `ps aux` | grep nginx` | Find a running service |
| Kill process by name | `pkill nginx` | Stops all nginx processes |
| Watch logs in real time | `tail -f /var/log/syslog` | Real-time log monitoring |
| Filter errors in logs | `tail -f app.log | grep ERROR` | Shows errors live |
| Count occurrences in file | `grep -c "error" app.log` | Count number of matches |
| Find IP addresses in logs | `grep -Eo '([0-9]{1,3}\.){3}[0-9]{1,3}' access.log` | Extract IP addresses |
| Extract first column | `awk '{print $1}' file.txt` | Prints column data |
| Extract usernames | `cut -d: -f1 /etc/passwd` | List system users |
| Unique sorted values | `sort file.txt | uniq` | Remove duplicates |
| Count unique entries | `sort file.txt | uniq -c` | Frequency count |
| Monitor CPU usage | `top` | Live CPU monitoring |
| Check open ports | `ss -tulnp` | Shows listening services |
| Ping server continuously | `ping google.com` | Check connectivity |
| Download file from URL | `curl -O https://example.com/file` | Fetch remote files |
| Get HTTP status | `curl -I https://example.com` | Check server response |
| Parse JSON | `curl api | jq '.data'` | Extract JSON field |
| Monitor command every 2 sec | `watch -n 2 df -h` | Refresh disk usage |
| Find failed SSH logins | `grep "Failed password" /var/log/auth.log` | Security check |
| Count requests by IP | `awk '{print $1}' access.log | sort | uniq -c | sort -nr` | Detect attackers |
| Backup directory | `tar -czvf backup.tar.gz /dir` | Create compressed backup |
| Extract tar archive | `tar -xzvf backup.tar.gz` | Restore backup |
| Check service status | `systemctl status nginx` | Verify service state |


-----------------------------------

**Powerful Pipelines**
```
ps aux | grep process
du -ah | sort -rh | head
awk '{print $1}' file | sort | uniq -c
tail -f log | grep ERROR
find . -type f -mtime +7
```
