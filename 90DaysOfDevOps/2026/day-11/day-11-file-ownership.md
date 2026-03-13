# File Ownership Challenge (chown & chgrp)

***COMMANDS***
### View ownership
ls -l filename

### Change owner only
sudo chown newowner filename

### Change group only
sudo chgrp newgroup filename

### Change both owner and group
sudo chown owner:group filename

### Recursive change (directories)
sudo chown -R owner:group directory/

### Change only group with chown
sudo chown :groupname filename

_________________________________________________________________________________________________

# Day 11 Challenge

## Understanding Ownership
1. Run `ls -l` in your home directory
Identify the owner and group columns
Check who owns your files

`
OUTPUT: -rw-r--r-- 1 owner group size date filename
`

## Basic chown Operations


```
Create file devops-file.txt
Check current owner: ls -l devops-file.txt
Change owner to tokyo (create user if needed)
Change owner to berlin
Verify the changes 
```


**Command**

```
$ touch devops-file.txt

$ ls -l devops-file.txt

$ chown  tokyo: devops-file.txt

$ chown  berln: devops-file.txt

$ ls -l
```
<img width="801" height="337" alt="image" src="https://github.com/user-attachments/assets/bb5f7667-f338-4dba-97e6-1ba899762e0d" />

## Basic chgrp Operations

```
Create file team-notes.txt
Check current group: ls -l team-notes.txt
Create group: sudo groupadd heist-team
Change file group to heist-team
Verify the change
```

**Command**

```
$ touch team-notes.txt

$ ls -l team-notes.txt

$ sudo groupadd heist-team

$ chown  :heist-team team-notes.txt

$ ls -l
```
<img width="831" height="141" alt="image" src="https://github.com/user-attachments/assets/a155bc41-0cc1-486f-b925-972b649761ab" />

## Combined Owner & Group Change 

```
Using chown you can change both owner and group together:
Create file project-config.yaml
Change owner to professor AND group to heist-team (one command)
Create directory app-logs/
Change its owner to berlin and group to heist-team
Syntax: sudo chown owner:group filename
```

**Command**

```
$ touch project-config.yaml

$ ls -l project-config.yaml

$ chown  professor:heist-team project-config.yaml

$ mkdir app-logs/

$ chown berlin:heist-team app-logs/

$ ls -l
```
<img width="966" height="184" alt="image" src="https://github.com/user-attachments/assets/852cf6f6-a7c2-4753-8ce2-6204b58c6c31" />

## Recursive Ownership

1. Create directory structure:

```
mkdir -p heist-project/vault
mkdir -p heist-project/plans
touch heist-project/vault/gold.txt
touch heist-project/plans/strategy.conf
```
2. Create group `planners`: `sudo groupadd planners`

3. Change ownership of entire `heist-project/ directory:`

Owner: `professor`

Group: `planners`

Use recursive flag (-R)

4. Verify all files and subdirectories changed:` ls -lR heist-project/`

**OUTPUT**
<img width="1046" height="781" alt="image" src="https://github.com/user-attachments/assets/bc3d3837-55c0-454d-9592-791b6b731b62" />

## Practice Challenge

```
Create users: tokyo, berlin, nairobi (if not already created)
Create groups: vault-team, tech-team
Create directory: bank-heist/
Create 3 files inside:
touch bank-heist/access-codes.txt
touch bank-heist/blueprints.pdf
touch bank-heist/escape-plan.txt

Set different ownership:

access-codes.txt → owner: tokyo, group: vault-team
blueprints.pdf → owner: berlin, group: tech-team
escape-plan.txt → owner: nairobi, group: vault-team

Verify: ls -l bank-heist/

```

**COMMANDS**

```
sudo useradd -m username

sudo groupadd vault-team | sudo groupadd tech-team

mkdir bank-heist/

```
--------

```
touch bank-heist/access-codes.txt

touch bank-heist/blueprints.pdf

touch bank-heist/escape-plan.txt

```
---------

```
sudo chown tokyo:vault-team acess-code.txt

sudo chown berlin:tech-team bluprints.txt

sudo chown nairobi:vault-team escape-plan.txt

```
<img width="982" height="514" alt="image" src="https://github.com/user-attachments/assets/8fea2d87-dad0-42ae-bf90-bf6623719684" />
