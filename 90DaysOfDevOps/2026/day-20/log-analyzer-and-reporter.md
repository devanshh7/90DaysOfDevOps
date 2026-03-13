# Day 20 — Bash Scripting Challenge

## Log Analyzer and Report Generator

---

#  Objective

Build a Bash automation script that:

* Analyzes server log files
* Detects errors and critical events
* Identifies top recurring issues
* Generates a structured summary report
* Archives processed logs

This project simulates real-world DevOps log monitoring tasks.

---

# Task 1: Input & Validation

## Requirement

* Accept log file path as argument
* Exit if no argument provided
* Exit if file does not exist

## Implementation

```bash
if [ -z "$1" ]; then
    echo "Usage: $0 <log_file>"
    exit 1
fi

LOG_FILE=$1

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: File does not exist"
    exit 1
fi
```

---

#  Task 2: Error Count

## Requirement

Count lines containing:

* ERROR
* Failed

## Implementation

```bash
ERROR_COUNT=$(grep -Ei "ERROR|Failed" "$LOG_FILE" | wc -l)
echo "Total Errors: $ERROR_COUNT"
```

---

# Task 3: Critical Events

## Requirement

* Print lines containing CRITICAL
* Show line numbers

## Implementation

```bash
echo "--- Critical Events ---"
grep -n "CRITICAL" "$LOG_FILE"
```

---

# Task 4: Top 5 Error Messages

## Requirement

* Extract lines containing ERROR
* Identify top 5 most frequent messages
* Sort by highest occurrence

## Implementation

```bash
grep "ERROR" "$LOG_FILE" \
| awk '{$1=$2=$3=""; print}' \
| sort \
| uniq -c \
| sort -rn \
| head -5
```

---

# Task 5: Summary Report Generation

## Requirement

Generate:

log_report_<date>.txt

Example:
log_report_2026-03-04.txt

Include:

* Date of analysis
* Log file name
* Total lines processed
* Total error count
* Top 5 error messages
* Critical events list

## Implementation

```bash
DATE=$(date +%Y-%m-%d)
REPORT="log_report_$DATE.txt"
TOTAL_LINES=$(wc -l < "$LOG_FILE")

{
echo "Log Analysis Report"
echo "Date: $DATE"
echo "Log File: $LOG_FILE"
echo "Total Lines: $TOTAL_LINES"
echo "Total Errors: $ERROR_COUNT"
echo ""
echo "--- Top 5 Error Messages ---"
echo "$TOP_ERRORS"
echo ""
echo "--- Critical Events ---"
echo "$CRITICAL_EVENTS"
} > "$REPORT"
```

---

# 🧩 Task 6 (Optional): Archive Processed Logs

## Requirement

* Create archive/ directory if not exists
* Move processed log file
* Print confirmation

## Implementation

```bash
mkdir -p archive
mv "$LOG_FILE" archive/
echo "Log file moved to archive/"
```

---

# Final Script: log_analyzer.sh

```bash
#!/bin/bash

# Task 1: Input Validation
if [ -z "$1" ]; then
    echo "Usage: $0 <log_file>"
    exit 1
fi

LOG_FILE=$1

if [ ! -f "$LOG_FILE" ]; then
    echo "Error: File does not exist"
    exit 1
fi

DATE=$(date +%Y-%m-%d)
REPORT="log_report_$DATE.txt"

echo "Analyzing file: $LOG_FILE"

# Task 2: Error Count
ERROR_COUNT=$(grep -Ei "ERROR|Failed" "$LOG_FILE" | wc -l)
TOTAL_LINES=$(wc -l < "$LOG_FILE")

# Task 3: Critical Events
CRITICAL_EVENTS=$(grep -n "CRITICAL" "$LOG_FILE")

# Task 4: Top 5 Errors
TOP_ERRORS=$(grep "ERROR" "$LOG_FILE" \
| awk '{$1=$2=$3=""; print}' \
| sort \
| uniq -c \
| sort -rn \
| head -5)

echo "Total Errors: $ERROR_COUNT"

echo "--- Critical Events ---"
echo "$CRITICAL_EVENTS"

echo "--- Top 5 Error Messages ---"
echo "$TOP_ERRORS"

# Task 5: Report Generation
{
echo "Log Analysis Report"
echo "Date: $DATE"
echo "Log File: $LOG_FILE"
echo "Total Lines: $TOTAL_LINES"
echo "Total Errors: $ERROR_COUNT"
echo ""
echo "--- Top 5 Error Messages ---"
echo "$TOP_ERRORS"
echo ""
echo "--- Critical Events ---"
echo "$CRITICAL_EVENTS"
} > "$REPORT"

echo "Report generated: $REPORT"

# Task 6: Archive
mkdir -p archive
mv "$LOG_FILE" archive/

echo "Log file moved to archive/"
```

---

# 🖥 Sample Console Output

```
Analyzing file: sample_log.log
Total Errors: 57

--- Critical Events ---
Line 84: CRITICAL Disk space below threshold
Line 217: CRITICAL Database connection lost

--- Top 5 Error Messages ---
45 Connection timed out
32 File not found
28 Permission denied
15 Disk I/O error
9 Out of memory

Report generated: log_report_2026-03-04.txt
Log file moved to archive/
```

---

# What I Learned

1. How to automate log analysis using grep, awk, sort, and uniq
2. How to generate structured reports using Bash redirection
3. Importance of log monitoring in DevOps and SRE
4. How to implement file validation and error handling
5. How to archive processed logs automatically

---

# Real-World Importance

This project simulates:

* Production log monitoring
* Incident detection
* Error pattern analysis
* Report generation automation
* Basic SIEM-like functionality using Bash

It demonstrates practical DevOps skills in automation and system reliability.
