# Breather & Revision
**1. Which 3 commands save you the most time right now, and why?**
- `mkdir`: it is easy to use nd we can file or dirctory easily while using it
- `useradd -m usrname` : this is the command used for adding new user and it is imp bcoz we can group userbased on groups and we can add those usrs to gropu they belong to..
- `chown owner filename | chowm owner:group file-name` : these cmd really help in real scenerio to give permissions and to change owner or group specifically.

**2. How do you check if a service is healthy? List the exact 2–3 commands you’d run first.**

- `systemctl status service-name`: this cmd will output whether the service is healthy or currenty running and active
- `journalctl -u servicename`: used to check the logs related to it

**3. How do you safely change ownership and permissions without breaking access? Give one example command.**

- `chmod -R 700 file.txt | chown -R new-owner:group file.txt `
  
**4. What will you focus on improving in the next 3 days?**
   `I will learn more about permissions and how these works in real world scenerio`
