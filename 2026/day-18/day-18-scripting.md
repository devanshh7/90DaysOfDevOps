# Shell Scripting: Functions & Slightly Advanced Concepts


# ✅ Task 1: Basic Functions

## 📄 File: functions.sh

### Requirements:
- [ ] Create function `greet`
- [ ] Create function `add`
- [ ] Call both functions
- [ ] Test with different inputs

### Test Output:


<img width="778" height="573" alt="image" src="https://github.com/user-attachments/assets/2b4cf605-a0df-4bc3-8e96-86662307f639" />

---------------------------------------

# ✅ Task 2: Disk & Memory Check

## 📄 File: disk_check.sh

### Requirements:
- [ ] Function `check_disk`
- [ ] Function `check_memory`
- [ ] Main section calling both
- [ ] Output formatted clearly

### Test Output:

<img width="736" height="618" alt="image" src="https://github.com/user-attachments/assets/5abdfb51-312c-414e-802b-a6f65362b8a1" />


---------------------------------------

# ✅ Task 3: Strict Mode

## 📄 File: strict_demo.sh

### Requirements:
- [ ] Use `set -euo pipefail`
- [ ] Test undefined variable
- [ ] Test failing command
- [ ] Test failing pipeline

### What I Learned:

- `set -e` → Exit immediately if any command fails.
- `set -u` → Exit if using undefined variables.
- `set -o pipefail` → Fail a pipeline if any command in it fails.

### Errors Observed:

Error observed was that if undefined variable was excuted then the command exit there and the rest code will not run as the scripts exits there.

To continue execution while testing errors, catch failures using ||, subshells ( ... ) || ..., or provide default values like ${name:-default}. This way, all parts of the script can run safely without stopping.

### Test Output:


<img width="802" height="749" alt="image" src="https://github.com/user-attachments/assets/a39f146f-0f60-46c3-bb79-ad785df99dce" />

---------------------------------------
# ✅ Task 4: Local Variables

## 📄 File: local_demo.sh

### Requirements:
- [ ] Function with `local` variables
- [ ] Function without `local`
- [ ] Show difference

### My Understanding:

It is good to use local inside the function because if we use sme variable outside the funct it will take value of it and cause problem!!

### Test Output:


<img width="689" height="557" alt="image" src="https://github.com/user-attachments/assets/16ad62db-d55a-4af3-871b-7e19386b6d33" />

---------------------------------------

# ✅ Task 5: System Info Reporter

## 📄 File: system_info.sh

### Requirements:
- [ ] Use `set -euo pipefail`
- [ ] Function: hostname & OS
- [ ] Function: uptime
- [ ] Function: disk usage
- [ ] Function: memory usage
- [ ] Function: top CPU processes
- [ ] Use `main()` function
- [ ] Clean formatted output

### Improvements I Can Add Later:
- [ ] Logging to file
- [ ] Add colors
- [ ] Add argument parsing
- [ ] Add email alert
- [ ] Convert to monitoring tool

### Final Output:


<img width="713" height="960" alt="image" src="https://github.com/user-attachments/assets/73fa96ae-6a46-4ba4-beeb-fd07c9ee78f3" />

<img width="1351" height="644" alt="image" src="https://github.com/user-attachments/assets/9f3dab0a-ed73-4241-a565-57d96e46aab8" />

