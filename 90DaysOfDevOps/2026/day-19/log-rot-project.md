# Project Overview: Log Rotation, Backup & Crontab

### WHAT is this project?

This project automates server maintenance tasks using shell scripting:

1. Log Rotation → compress old logs, delete very old logs

2. Backup → create timestamped backups and clean old backups

3. Crontab → schedule tasks automatically (it is complimentory, not compulsory)

4. Maintenance Script → combine everything and log output

In simple words:

👉 You are creating automation scripts that maintain a Linux server automatically

### WHY do we need this?

**Servers generate:**

- Huge log files

- Important application data

- Continuous system output

**Without automation:**

- Disk gets full ❌

- Server crashes ❌

- Data loss risk ❌

- Manual work required ❌

Automation solves this.

### HOW does it work technically?

You will use:

- `bash scripting `→ to automate tasks

- `find `→ locate files based on age

- `gzip` → compress files

- `tar` → create backups

- `cron` → schedule automatic execution

- `date` → create timestamps

### WHY is it important in DevOps?

This is used in real companies daily.

DevOps Engineers automate:

- Log rotation

- Backups

- Monitoring

- Maintenance

This is a **core DevOps skill.**

-------------------------------------------------------

### TASK 1: Log Rotation Script

#### WHAT it does

Script will:

- Take log directory as input

- Compress logs older than 7 days

- Delete compressed logs older than 30 days

- Show number of files processed

#### How to create:

*I am not having  any log file inside my /var/lof/my-app , so there is way to practice that i follow was i make a ne dir in /var/log as /demo-app and write a script that will make a n number of file taking user input and modify its last date so that it will perform log rotation.*

**Command**

`sudo mkdir /var/log/demo-app` : To create demo-app/ directory

`sudo vim log_gen.sh`: created a script that should create number of logs by taking input fromuser and modiy its date also.

<img width="625" height="283" alt="image" src="https://github.com/user-attachments/assets/003834b5-4613-419c-96cc-9e5b8e1d9e49" />

**Script code**
<img width="720" height="483" alt="image" src="https://github.com/user-attachments/assets/fa584f80-40a0-460b-a6dc-4e94f7091bb0" />

```
#!/bin/bash
create_logs() {
#           read -p "Enter directory to create logs in: " LOG_DIR
#           read -p "How many log files to create? " COUNT

#           mkdir -p "$LOG_DIR"

#           for ((i=1; i<=COUNT; i++))
#               do
#                       FILE="$LOG_DIR/demo$i.log"
#                       echo "This is log file $i" > "$FILE"
#                       echo "Created: $FILE"
#               done

#               echo "Total $COUNT log files created in $LOG_DIR"

        read -p "Enter directory to create logs in: " LOG_DIR
        read -p "How many log files to create? " COUNT
        LOG_DIR=$(eval echo "$LOG_DIR")
        LOG_DIR=$(realpath -m "$LOG_DIR")

        mkdir -p "$LOG_DIR"

        echo "Creating logs inside: $LOG_DIR"
        echo "--------------------------------"

        LAST_NUM=$(ls "$LOG_DIR"/demo*.log 2>/dev/null | \
                        sed -E 's/.*demo([0-9]+)\.log/\1/' | \
                                sort -n | tail -1)

        LAST_NUM=${LAST_NUM:-0}
        for ((i=1; i<=COUNT; i++))
        do
                NEW_NUM=$((LAST_NUM + i))
                FILE="$LOG_DIR/demo$NEW_NUM.log"
                echo "This is log file $NEW_NUM" > "$FILE"
                echo "Created: $FILE"
        done

        echo "--------------------------------"
        echo "Total $COUNT new log files created."

}


modify_dates() {
        read -p "Enter directory where logs exist: " LOG_DIR
        read -p "Make logs how many days old? " DAYS

        if [ ! -d "$LOG_DIR" ]; then
                echo "Directory does not exist!"
                return
        fi

        for file in "$LOG_DIR"/*.log
        do
                if [ -f "$file" ]; then
                touch -d "$DAYS days ago" "$file"
                echo "Modified date: $file"
        fi                                                                                                                     done

        echo "All .log files are now $DAYS days old."


}

echo "1. Create Log Files"
echo "2. Modify Log File Dates"
read -p "Choose an option (1 or 2): " OPTION

case $OPTION in
        1) create_logs ;;
        2) modify_dates ;;
        *) echo "Invalid option!" ;;
esac
```
**ISSUE I FACED:-**
*Issue with the the commented code is when we run this script once it will create suppose 6 logs file but when we run it twice for 5 more file it again it will run error bcoz it will again gonna create files from demo1.log to demo5.log .. which already exists .*

*So i updated the log gennerator func code to see existing logs and then continue making lgs with the next number to it.*

<img width="850" height="564" alt="image" src="https://github.com/user-attachments/assets/1e34115a-8132-42bf-a589-216ce7a042e9" />

-------------------------------------------------

## NOW TASK-1

**FLOW DIAGRAM**
<img width="519" height="444" alt="image" src="https://github.com/user-attachments/assets/df3f165e-8a42-4caa-9742-2171603fdcb2" />

- Create file: `/home/your-usr` - create a .sh file in you home user

```
vim log_rotate.sh
```

- Paste this:

```
#!/bin/bash
#
if [ -z "$1" ]; then
            echo "Usage: $0 <log_directory>"
                exit 1
fi

LOG_DIR=$1
if [ ! -d "$LOG_DIR" ]; then
            echo "Error: Directory does not exist"
                exit 1
fi

compressed=0
deleted=0

echo "Starting log rotation in $LOG_DIR"
for file in $(find "$LOG_DIR" -name "*.log" -mtime +7)
do
        gzip "$file"
        ((compressed++))
done

# DElete .gz file > 30 days
#
for file in $(find "$LOG_DIR" -name "*.log" -mtime +30)
do
        rm "$file"
        ((deleted++))
done

echo "Compressed Files: $compressed"
echo "Deleted Files: $deleted"

exit 0
```
<img width="810" height="548" alt="image" src="https://github.com/user-attachments/assets/e2b249a3-1d62-4b4f-b215-8ac4e2b2e7d5" />

**OUTPUT**
- Compressed & Deleted result will show
  <img width="1022" height="143" alt="image" src="https://github.com/user-attachments/assets/04ad178f-c409-46af-adcf-d6fda88c7608" />

--------------------------------------------------------

### Task 2: Server Backup Script

#### WHAT it does

Script will:

- Takes source directory
- Creates backup archive
- Adds timestamp
- Deletes old backups

  <img width="475" height="458" alt="image" src="https://github.com/user-attachments/assets/bd808842-c5a9-4d65-9ef0-430ca60bb2e4" />


#### WHY important

- Backups protect data from:
- Server crash
- Accidental deletion
- Cyber attack
#### HOW to create

`vim or nano backup.sh`


- **Paste this:**

```
#!/bin/bash
#
# Check argument
if [ $# -ne 2 ]; then
            echo "Usage: $0 <source_directory> <backup_destination>"
                exit 1
fi

SOURCE=$1
DEST=$2
echo "SOURCE: '$SOURCE'"
echo "DEST: '$DEST'"

#check if source dir exists
if [ ! -d "$SOURCE" ]; then
            echo "Error: Source Directory does not exist"
            exit 1
fi

# Create destination dir if not exists

mkdir -p "$DEST"

TIMESTAMP=$(date +%Y-%m-%d)
BACKUP_FILE="$DEST/backup-$TIMESTAMP.tar.gz"

#create backup file
tar -czf "$BACKUP_FILE" "$SOURCE"

# create BACKUP
#
if [ -f "$BACKUP_FILE" ];then
        echo "BAckup created successfully.."
        echo "File : $BACKUP_FILE"
        echo "SIZE: $(du -h $BACKUP_FILE | cut -f1)"
else
        echo "Backup Failed"
        exit 1
fi

# DELETE bkp older than 14 days
#
DELETED=$(find "$DEST" -name "*.tar.gz" -mtime +14 -print -delete | wc -l)

if [ "$DELETED" -gt 0 ]; then
            echo "$DELETED old backups deleted."
    else
                echo "No old backups to delete."
fi
```
<img width="1084" height="178" alt="image" src="https://github.com/user-attachments/assets/8a6f4ee7-6a63-41ae-9718-17fd6b2e8b4c" />





----------------------------------------------------------------

### Task 3: Crontab

Cron automatically runs scripts at specific times.


*It's on your hand to whether use cron or not ,I have written how to run cron jon automatically 2AM Daily..*

#### View cron jobs

`crontab -l `

#### Edit cron

`crontab -e`

### Cron entries required


#### Run log rotation daily at 2 AM

`0 2 * * * /path/to/log_rotate.sh /var/log/myapp`

#### Run backup every Sunday at 3 AM

`0 3 * * 0 /path/to/backup.sh /home/devansh /home/devansh/backups`

#### Run health check every 5 minutes

`*/5 * * * * /path/to/health_check.sh`

<img width="1200" height="630" alt="image" src="https://github.com/user-attachments/assets/0c04d868-a791-45b4-9497-1381c47d792e" />

----------------------------------------------------------------


