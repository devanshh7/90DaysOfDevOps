# Cloud Server Setup: Docker, Nginx & Web Deployment using AWS

### Part 1: Launch Cloud Instance & SSH Access 

- First of all create an EC2 instnce of your choice.
- I have created EC2 having OS: `Ubuntu` , Instance type: `t2.micro ot t3.micro` (AWS free tier), Storage 15 gb for safer side, Key-pair: .pem
- Now, your instance is created and for connecting through using SSH ,you need to select SSH client under which there are some steps written follow those.

<img width="753" height="311" alt="image" src="https://github.com/user-attachments/assets/fbe6d27b-342c-4d3d-b6af-28c07717236c" />

- Now open Git bash if you have and if not install it.
- Check whether SSH installed or not
  
  <img width="685" height="183" alt="image" src="https://github.com/user-attachments/assets/f509266f-dbb8-40cc-99e4-8ee92c47e310" />

- Locate your .pem file download in whichever foder . In my case it is `Linux-practice-key.pem`

  <img width="410" height="119" alt="image" src="https://github.com/user-attachments/assets/a9660421-d856-47fb-8c9a-553123384043" />

- Run this command, if necessary, to ensure your key is not publicly viewable.
Command: `chmod 400 "Linux-practice-key.pem"`

- Now connect it using command `ssh -i "Linux-practice-key.pem" ubuntu@ec2-3-238-248-36.compute-1.amazonaws.com`

<img width="779" height="750" alt="image" src="https://github.com/user-attachments/assets/70dc1710-00dd-4012-b344-3b32fe131674" />

### Part 2: Install Docker & Nginx (20 minutes)

- Update System using command : `sudo apt update`

  <img width="643" height="118" alt="image" src="https://github.com/user-attachments/assets/e8b5813b-669e-42d1-b349-25a8fcb483dd" />

- Verify Nginx is running

  command : `systemctl status nginx & docker`

  <img width="660" height="268" alt="image" src="https://github.com/user-attachments/assets/9c52fec9-039e-42a1-b7d6-8efe545499b5" />

  <img width="653" height="206" alt="image" src="https://github.com/user-attachments/assets/1b2c4bef-e867-42de-9693-f511ea1e5606" />


### Part 3: Security Group Configuration
Enable Inbound Rules and add port 80 and type HTTP

<img width="919" height="709" alt="image" src="https://github.com/user-attachments/assets/69eaf7e0-5480-42c4-b0c7-dee9c53960cd" />

- Now I will see nginx while opening my browser

<img width="707" height="374" alt="image" src="https://github.com/user-attachments/assets/878c32b1-89ec-4b28-ba65-ba68dde5d518" />

### Part 4: Extract Nginx Logs 
- Step 1: View Nginx Logs

<img width="725" height="264" alt="image" src="https://github.com/user-attachments/assets/e1be3aeb-d289-4023-95b8-9018e6902e5f" />

- Step-2 : Firstly on my ssh window, created nginx-logs.txt and copy `/var/log/nginx/access/log /var/log/nginx/error.log`

  <img width="768" height="549" alt="image" src="https://github.com/user-attachments/assets/5afd3e16-4901-4c41-ba51-5ef7b76bc060" />

- Step-3 : Open local terminal and use this command `# scp -i your-key.pem ubuntu@<your-instance-ip>:~/nginx-logs.txt .`
  
  Example: For my case: `$ scp -i Linux-practice-key.pem ubuntu@3.238.248.36:~/nginx-logs.txt .`

<img width="793" height="737" alt="image" src="https://github.com/user-attachments/assets/67561920-204a-42c1-b030-48725234ad23" />

## Commands Used

1. sudo apt update
2. sudo apt-get install ssh
3. Create EC2 and connect to ssh

  - cd 'path' : where .pem file is present
  - chmod 400 to .pem file
    
4. SSH connect command
5. Copy logs of nginx and then remotely download in local system
  - In EC2 server : `touch nginx-logs.txt`
  - Copy commnad: `sudo cat /var/log/nginx/access.log /var/log/nginx/error.log | tee nginx-logs.txt `
  - `cat nginx-logs.txt`

6. Open local windows terminal

   - write this cmd ` scp -i your-key.pem ubuntu@<your-instance-ip>:~/nginx-logs.txt .
`

## Challenges Faced

The challange that I faced was I created nginx-logs.txt in my EC2 server and than I was not aware that I have to first create log.txt and copy `access.log/` & `error.log`.

Next , I have to open local terminal and then run cmd `scp -i Linux-practice-key.pem ubuntu@3.238.248.36:~/nginx-logs.txt .` 

- `scp` : Secure Copy Protocol , Used to copy files securely between machines over SSH
- `-i Linux-practice-key.pem`: -i = identity file , Specifies the private SSH key to authenticate to the remote server.
👉 AWS EC2 requires a key file instead of a password.

- `ubuntu@3.238.248.36`: ubuntu → username on the EC2 instance

  3.238.248.36 → public IP address of the EC2 instance

  This tells scp which user and which server to connect to.

- `:`: Separator between remote server and remote file path ,
    Ex `user@server:/path/to/file`

- `~/nginx-logs.txt`: `~` → home directory of the ubuntu user (/home/ubuntu)

                nginx-logs.txt → file you want to copy

                👉 This is the source file on the remote server.


- `. (dot)` : Destination path on your local machine , `.` means current directory

  `“Securely copy nginx-logs.txt from the home directory of the ubuntu user on the EC2 server at 3.238.248.36 into my current local directory using this SSH key.”`
  
## What I Learned

Learned how to securely copy files from a remote cloud server to my local machine using scp and SSH key authentication.
  
