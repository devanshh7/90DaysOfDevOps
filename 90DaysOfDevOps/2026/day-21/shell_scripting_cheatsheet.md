# Shell Scripting Quick Reference Table

| Topic | Key Syntax | Example |
|------|------------|---------|
| Shebang | `#!/bin/bash` | `#!/bin/bash` |
| Run Script | `chmod +x script.sh` | `./script.sh` |
| Comment | `# comment` | `# This is a comment` |
| Variable | `VAR="value"` | `NAME="DevOps"` |
| Use Variable | `$VAR` | `echo $NAME` |
| Read Input | `read var` | `read username` |
| Script Argument | `$1 $2` | `./script.sh file.txt` |
| Number of Args | `$#` | `echo $#` |
| All Args | `$@` | `echo $@` |
| Exit Status | `$?` | `echo $?` |

---

# Conditionals & Operators

| Topic | Syntax | Example |
|------|--------|---------|
| String Equal | `[ "$a" = "$b" ]` | `[ "$name" = "admin" ]` |
| String Not Equal | `[ "$a" != "$b" ]` | `[ "$user" != "root" ]` |
| Empty String | `[ -z "$var" ]` | `[ -z "$name" ]` |
| Not Empty | `[ -n "$var" ]` | `[ -n "$input" ]` |
| Equal (int) | `[ $a -eq $b ]` | `[ $x -eq 10 ]` |
| Not Equal | `[ $a -ne $b ]` | `[ $x -ne 5 ]` |
| Less Than | `[ $a -lt $b ]` | `[ $x -lt 100 ]` |
| Greater Than | `[ $a -gt $b ]` | `[ $x -gt 50 ]` |
| File Exists | `[ -e file ]` | `[ -e config.txt ]` |
| File | `[ -f file ]` | `[ -f script.sh ]` |
| Directory | `[ -d dir ]` | `[ -d logs ]` |
| Readable | `[ -r file ]` | `[ -r data.txt ]` |
| Writable | `[ -w file ]` | `[ -w report.txt ]` |

---

# Conditional Statements

| Topic | Syntax | Example |
|------|--------|---------|
| If | `if [ cond ]; then` | `if [ -f file ]; then` |
| If Else | `if ... else` | `if [ $a -gt 10 ]; then echo big; else echo small; fi` |
| If Elif | `elif` | `elif [ $a -eq 10 ]` |
| Logical AND | `&&` | `[ -f file ] && echo "exists"` |
| Logical OR | `||` | `[ -f file ] || echo "missing"` |
| NOT | `!` | `[ ! -f file ]` |
| Case | `case ... esac` | `case $opt in start) ... ;; esac` |

---

# Loops

| Topic | Syntax | Example |
|------|--------|---------|
| For Loop | `for i in list` | `for i in 1 2 3` |
| C-style For | `for ((i=0;i<5;i++))` | `for ((i=1;i<=5;i++))` |
| While Loop | `while [ cond ]` | `while [ $i -lt 5 ]` |
| Until Loop | `until [ cond ]` | `until [ $x -gt 10 ]` |
| Break | `break` | `break` |
| Continue | `continue` | `continue` |
| Loop Files | `for f in *.log` | `for f in *.log; do echo $f; done` |
| Read Lines | `while read line` | `cat file | while read line` |

---

# Functions

| Topic | Syntax | Example |
|------|--------|---------|
| Define Function | `name() {}` | `greet() { echo "Hello"; }` |
| Call Function | `name` | `greet` |
| Function Argument | `$1 $2` | `echo $1` |
| Return Code | `return` | `return 0` |
| Output Value | `echo` | `result=$(func)` |
| Local Variable | `local var` | `local count=10` |

---

# Text Processing Commands

| Command | Syntax | Example |
|-------|-------|--------|
| grep | `grep pattern file` | `grep -i "error" log.txt` |
| awk | `awk '{print $1}'` | `awk -F: '{print $1}' /etc/passwd` |
| sed | `sed 's/old/new/g'` | `sed -i 's/foo/bar/g' file.txt` |
| cut | `cut -d delim -f` | `cut -d: -f1 /etc/passwd` |
| sort | `sort file` | `sort -nr numbers.txt` |
| uniq | `uniq` | `uniq -c file.txt` |
| tr | `tr a-z A-Z` | `echo hi | tr a-z A-Z` |
| wc | `wc` | `wc -l file.txt` |
| head | `head -n` | `head -n 10 log.txt` |
| tail | `tail -n` | `tail -f log.txt` |

---

# Debugging & Error Handling

| Topic | Syntax | Example |
|------|--------|---------|
| Exit Code | `$?` | `echo $?` |
| Exit Script | `exit` | `exit 1` |
| Exit on Error | `set -e` | `set -e` |
| Unset Variable Error | `set -u` | `set -u` |
| Pipe Fail | `set -o pipefail` | `set -o pipefail` |
| Debug Mode | `set -x` | `set -x` |
| Trap | `trap` | `trap 'cleanup' EXIT` |
