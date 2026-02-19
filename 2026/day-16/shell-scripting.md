# Shell Scripting Basics

*Shell Scriptiong are the set or combination of multiple shell commands writeen in .sh file*

[**Shebang: #!/bin/bash**]

The shebang tells your system which interpreter to use to run the script. Without it, your script may fail or run in the wrong shell.

You have to put shebang at the start of your script.


## Task 1: Your First Script
[
Create a file hello.sh

Add the shebang line #!/bin/bash at the top

Print Hello, DevOps! using echo

Make it executable and run it
]

`CODE:`

```
#!/bin/bash

echo "EHlooo DEvops!!!"
```
<img width="527" height="81" alt="image" src="https://github.com/user-attachments/assets/2bd483a6-d128-42e4-a330-a574e54b27a8" />


## Task 2: VAriables
[
Create variables.sh with:

A variable for your NAME

A variable for your ROLE (e.g., "DevOps Engineer")

Print: Hello, I am <NAME> and I am a <ROLE>

Try using single quotes vs double quotes — what's the difference?
]

`CODE:`

<img width="609" height="196" alt="image" src="https://github.com/user-attachments/assets/d43b6d22-118a-49bb-b8b9-f2ac3d13ece6" />

## Task 3: User Input with read
[
Create greet.sh that:

Asks the user for their name using read

Asks for their favourite tool

Prints: Hello <name>, your favourite tool is <tool>
]

`CODE:`
<img width="627" height="303" alt="image" src="https://github.com/user-attachments/assets/dc09b789-24d3-412a-ab3c-048965fc29a3" />


## Task 4: If-Else Conditions
[
1. Create check_number.sh that:

Takes a number using read

Prints whether it is positive, negative, or zero

2. Create file_check.sh that:

Asks for a filename

Checks if the file exists using -f

Prints appropriate message
]

`CODE:`

<img width="724" height="475" alt="image" src="https://github.com/user-attachments/assets/1311ffea-9264-4bd6-95fd-56927609892b" />

**File Check**
<img width="823" height="376" alt="image" src="https://github.com/user-attachments/assets/597604c6-ca5d-49fb-b6c2-ef8713ec672d" />

## Task 4: If-Else Conditions
[
Stores a service name in a variable (e.g., nginx, sshd)

Asks the user: "Do you want to check the status? (y/n)"

If y — runs systemctl status <service> and prints whether it's active or not

If n — prints "Skipped."
]

`CODE:`

<img width="650" height="395" alt="image" src="https://github.com/user-attachments/assets/dc2dc8b3-5eb4-415c-a2f0-66495e3d4593" />
