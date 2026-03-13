# Linux User & Group Management Challenge
### Task 1: Create Users
Create three users with home directories and passwords
- tokyo : `useradd tokyo`
- berlin : `useradd berlin`
- professor : `useradd professor`

 <img width="681" height="426" alt="image" src="https://github.com/user-attachments/assets/6ed270f8-5f2d-4ea1-ad28-9ec0c1484e60" />

<img width="676" height="417" alt="image" src="https://github.com/user-attachments/assets/47d446f7-b5d5-4f3a-afe8-8b6d8011d6e2" />

<img width="703" height="394" alt="image" src="https://github.com/user-attachments/assets/787c74a6-6f43-4a75-86e1-5d984563d9fe" />


### Task 2: Create Groups
`sudo groupadd groupname` - is the command to create a new group
**Create two groups:**
- developers : command `sudo groupadd developers` 
- admins: command `sudo groupadd admins`

<img width="485" height="79" alt="image" src="https://github.com/user-attachments/assets/669a267a-70a8-439e-9dae-4a4fddba11c6" />

` getent group groupname || cat /etc/group `
- command used to verify the group

<img width="448" height="132" alt="image" src="https://github.com/user-attachments/assets/2ebbb636-3d62-4ca1-92c9-078fa8fd5edc" />

  
### Task 3: Assign to Groups 

Assign users:

tokyo → developers
berlin → developers + admins (both groups)
professor → admins


**Commands**

`sudo usermod -aG developers,admins berlin` : -a (append) and G (means to group)
`sudo usermod -aG admins professor`
`sudo usermod -aG developers tokyo`
`cat /etc/group`

<img width="481" height="107" alt="image" src="https://github.com/user-attachments/assets/d88e1006-3099-4d18-9098-51c080fc3d59" />

<img width="379" height="110" alt="image" src="https://github.com/user-attachments/assets/57d808aa-e2e9-4af2-b3ed-e2f250cc5da1" />

### Task 4: Shared Directory
1. Create Directory : /opt/dev-project

<img width="522" height="84" alt="image" src="https://github.com/user-attachments/assets/61a17468-ca9d-4da3-80a1-4ac844b308e1" />


2. Set group owner to developers

**Commands:**
` sudo chown :developers /opt/dev-project/`
`ls -ld /opt/dev-project/`
<img width="645" height="70" alt="image" src="https://github.com/user-attachments/assets/1219f44c-e823-469d-adbd-86287a9cb1ad" />


3. Set permissions to 775 (rwxrwxr-x)
**Commands:**

`chmod 775 /opt/dev-project`- it changes the permissions to 7(owner-rwx) 7(gropu-rwx) 5(others- r-x)
We used cmd `chmod 2775 /opt/dev-project`

“To ensure files created in a shared directory inherit the correct group, I enabled the setgid bit using chmod 2775 directory.”

<img width="602" height="61" alt="image" src="https://github.com/user-attachments/assets/423f2fe7-d15d-4d02-a799-7afcab8f6caa" />

4. Test by creating files as tokyo and berlin

**Commands**
`su - tokyo
touch /opt/dev-project/test1.txt
exit

su - berlin
touch /opt/dev-project/test2.txt
exit`

**Final Verification*

`ls -l /opt/dev-project`


<img width="606" height="346" alt="image" src="https://github.com/user-attachments/assets/62a6e9d7-375c-43e9-a046-a178fce3a7cc" />


### Task 5: Team Workspace
```
Create user nairobi with home directory
Create group project-team
Add nairobi and tokyo to project-team
Create /opt/team-workspace directory
Set group to project-team, permissions to 775
Test by creating file as nairobi
```

***Commands:***
1. `sudo useradd -m nairobi`

<img width="526" height="81" alt="image" src="https://github.com/user-attachments/assets/3bf97251-278e-4acc-a1f6-f3253a3dafe4" />

2. `sudo groupadd project-team `
<img width="517" height="40" alt="image" src="https://github.com/user-attachments/assets/e839cd8c-390f-48b1-a4bb-1b958e501c6d" />

<img width="323" height="145" alt="image" src="https://github.com/user-attachments/assets/454dac47-7985-46dd-a3dd-e63ac4669c7c" />

3. `sudo usermod -aG project-team nairobi,tokyo `
<img width="823" height="58" alt="image" src="https://github.com/user-attachments/assets/b09158e9-856f-4c67-9ffb-c7ad87f75ab8" />

<img width="400" height="215" alt="image" src="https://github.com/user-attachments/assets/a57d8fb5-6e72-413a-a88c-8330a991f8cd" />

4. `mkdir -p /opt/team-workspace`

<img width="557" height="69" alt="image" src="https://github.com/user-attachments/assets/1c0589f7-f369-461f-bde2-132b1490c190" />

5. - `sudo chown :project-team /opt/team-workspace`
   - `sudo chmod 2775 /op/team-workspace `

<img width="649" height="185" alt="image" src="https://github.com/user-attachments/assets/11fad318-1ba3-492a-ada7-4a53aeed5173" />


6. `su - nairobi
touch /opt/team-workspace/nairobi-file.txt
exit
`
<img width="626" height="103" alt="image" src="https://github.com/user-attachments/assets/d4164da9-e4b4-445f-a3f2-a6cdd9eec7f6" />

7. Verify:
- `ls -ld /opt/team-workspace
ls -l /opt/team-workspace

<img width="598" height="96" alt="image" src="https://github.com/user-attachments/assets/9e9d4887-b060-4333-9626-58263d73cceb" />

# Lesson learne from this user and group functions.

*I learned that using chmod 2XXX(permisions like : chmod 2400) sets the setgid bit, which ensures that all new files created inside a directory inherit the directory’s group instead of the user’s primary group. The special permission bits are 4 (setuid), 2 (setgid), and 1 (sticky bit), and they provide advanced control over file and directory behavior in Linux.* 

OUTPUT- When you create file under durectory by switching to other user

`ls -l /opt/team-workspace`

- Now it should show:

-rw-rw-r-- 1 nairobi project-team ...
