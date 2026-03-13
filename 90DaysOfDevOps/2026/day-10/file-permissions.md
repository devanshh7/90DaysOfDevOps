# File Permissions & File Operations Challenge

## Files Created
1. Created file `devops.txt` using `touch`
- `touch devops.txt`

2. Created `notes.xt` with some contents and read using `cat` or write using `echo`
- `vim notes.txt` then when you are done--> esc : wq
- `cat notes.txt` 
3.  Create `script.sh` using `vim` with content `echo "hello devops"`
- `vim script.sh
   echo "hello devops" `
- `ls -l`: see permissions

<img width="711" height="565" alt="image" src="https://github.com/user-attachments/assets/72dae9a3-5d6a-4245-9876-c62e0a5af879" />

## Read Files
- Display first 5 lines in`/etc.passwd` : using ` head -5 /etc/passwd `
- Display last 5 lines in`/etc.passwd` : using ` tail -5 /etc/passwd `

<img width="622" height="242" alt="image" src="https://github.com/user-attachments/assets/ed47c648-26c1-4fd5-b8ba-f75385acd258" />

## Permission Changes
- Check permissions using >>
  `ls -l devops.txt notes.txt script.sh `
- Modify permission
  - Make `script.sh` executable >> `chmod 700 script.sh`
  - Set `devops.txt` to Read-only >> using `chmod 400 devops.txt`
  - Set `notes.txt ` to 640 >> using `chmod 640 notes.txt`
  - Create dorectory with permission 755 >>
    `mkdir project/
    chmod 755 project/`

    <img width="507" height="98" alt="image" src="https://github.com/user-attachments/assets/31f48ffd-b1ed-42dd-8683-207487bd902f" />

- Making script.sh executable
<img width="506" height="100" alt="image" src="https://github.com/user-attachments/assets/ef925550-61d0-49c4-8c5f-5929b868d3d0" />

<img width="617" height="41" alt="image" src="https://github.com/user-attachments/assets/168a4696-43b3-47a3-87a7-a7fe62bb1bf6" />

- Set `devops.txt` to read only
  <img width="650" height="123" alt="image" src="https://github.com/user-attachments/assets/98f7a1cf-667e-49f3-b501-0f9369ea8385" />
- Set `notes.txt` to 640
  <img width="487" height="106" alt="image" src="https://github.com/user-attachments/assets/7c715ec3-1c53-4d64-b9f1-f0b1b3b4f37b" />

  <img width="645" height="124" alt="image" src="https://github.com/user-attachments/assets/ef69f4b6-a897-4fdf-b566-15df550d68e9" />

## Testing Permissions
1. Try writing to a read-only file - what happens?
<img width="737" height="45" alt="image" src="https://github.com/user-attachments/assets/d4883e75-5017-42e8-b5fc-16f82128e872" />

2. Try executing a file without execute permission
<img width="562" height="84" alt="image" src="https://github.com/user-attachments/assets/bb7044d7-d5c7-4936-84d8-c100ec2157e4" />

3. Document the error messages
- `$ echo "added one line" > devops.txt
bash: devops.txt: Permission denied`
- `$ sudo ./demo.sh
sudo: ./demo.sh: command not found
      OR
$ ./demo.sh
bash: ./demo.sh: Permission denied`

## What I Learned
- How to provide diff file permissions to folder or file
- We cannot execute the command which we do not have permission to do.
