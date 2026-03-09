# Docker Volumes & Networking – Progress Report


### Objective

Understand how Docker handles:

* **Data persistence using Volumes**
* **Container communication using Docker Networks**

Containers are **ephemeral**, meaning when they are removed, their data is also removed.
Docker volumes and networking solve this problem.

---

# Task 1 – The Problem (Container Data Loss)

### Run a PostgreSQL Container

```bash
docker run --name mysql-test \
-e MYSQL_ROOT_PASSWORD=admin \
-d mysql:8
```
<img width="849" height="360" alt="image" src="https://github.com/user-attachments/assets/bc25d357-36b7-4613-a1d7-c9ff88d5fc49" />

### Enter the Container

```
docker exec -it mysql-test mysql -u root -p

Enter password:
admin
```
<img width="795" height="333" alt="image" src="https://github.com/user-attachments/assets/2e99f09c-73f7-4021-86a2-86b3f7e9bbed" />

### Create Sample Data

```
CREATE DATABASE devops_db;
USE devops_db;
```

```sql
CREATE TABLE students (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(50)
);

INSERT INTO students (name) VALUES ('Devansh'), ('Docker');
```
<img width="726" height="545" alt="image" src="https://github.com/user-attachments/assets/85448bac-cf16-40ad-aa36-1d574533867c" />

### Stop and Remove Container

```bash
docker stop mysql-test
docker rm mysql-test
```

### Run a New Container

```bash
docker run --name postgres-test \
-e POSTGRES_PASSWORD=admin \
-d postgres
```

<img width="924" height="516" alt="image" src="https://github.com/user-attachments/assets/d43705a8-babb-4c2f-9c8e-5374e9ea348a" />

Here we can see `devops_db` is gone , no such database was there.
### Observation

The table and inserted data were **lost**.

### Reason

Containers store data **inside their filesystem**.
When the container is removed, the filesystem is deleted.

---

# Task 2 – Named Volumes (Persistent Storage)

### Create a Volume

```bash
docker volume create postgres-data
```

Verify:

```bash
docker volume ls
```

### Run Postgres with Volume

```bash
docker run --name postgres-vol \
-e POSTGRES_PASSWORD=admin \
-v postgres-data:/var/lib/postgresql/data \
-d postgres
```

### Add Data

```bash
docker exec -it postgres-vol psql -U postgres
```

```sql
CREATE TABLE test_data (
id INT AUTO_INCREMENT PRIMARY KEY,
name VARCHAR(50)
);

INSERT INTO test_data (name) VALUES ('Docker Volume');
```

### Remove Container

```bash
docker stop postgres-vol
docker rm postgres-vol
```

### Run New Container Using Same Volume

```bash
docker run --name mysql-vol2 \
-e MYSQL_ROOT_PASSWORD=admin \
-v mysql-data:/var/lib/postgresql/data \
-d mysql:8
```
<img width="1077" height="417" alt="image" src="https://github.com/user-attachments/assets/72c5fffa-3354-4d8d-b389-bbe3450ed9a7" />

### Result

The data **still exists** because it is stored in the **Docker volume**.

<img width="876" height="850" alt="image" src="https://github.com/user-attachments/assets/81abd71b-54ec-453d-a29b-54416c8c82e2" />

### Verify Volume

```bash
docker volume ls
```

```bash
docker volume inspect mysql-data
```
<img width="715" height="154" alt="image" src="https://github.com/user-attachments/assets/f56a9157-0d88-4e0b-85e2-ea2ecfe46e10" />

---

# Task 3 – Bind Mounts

### Create a Folder

```bash
mkdir nginx-site
cd nginx-site
```

Create file:

```
index.html
```

Example content:

```html
<h1>Hello from Docker Bind Mount</h1>
```

### Run Nginx Container

```bash
docker run -d \
-p 8080:80 \
-v $(pwd):/usr/share/nginx/html \
--name nginx-bind \
nginx
```

Open browser:

```
http://localhost:8080
```

Edit `index.html` and refresh the page — changes appear instantly.

### Difference Between Named Volume and Bind Mount

| Feature    | Named Volume            | Bind Mount     |
| ---------- | ----------------------- | -------------- |
| Managed by | Docker                  | Host system    |
| Storage    | Docker internal storage | Host directory |
| Best for   | Databases               | Development    |

---

# Task 4 – Docker Networking Basics

### List Networks

```bash
docker network ls
```

Default networks include:

```
bridge
host
none
```

### Inspect Default Bridge

```bash
docker network inspect bridge
```

### Run Two Containers

```bash
docker run -dit --name container1 ubuntu
docker run -dit --name container2 ubuntu
```

### Ping by Name

```bash
docker exec -it container1 ping container2
```

Result: **Fails**

### Ping by IP

```bash
docker inspect container2
```

Get the container IP and run:

```bash
docker exec -it container1 ping <container-ip>
```

Result: **Success**

---

# Task 5 – Custom Networks

### Create Network

```bash
docker network create my-app-net
```

### Run Containers

```bash
docker run -dit --name app1 --network my-app-net ubuntu
docker run -dit --name app2 --network my-app-net ubuntu
```

### Test Communication

```bash
docker exec -it app1 ping app2
```

Result: **Success**

### Why It Works

Custom networks provide **automatic DNS resolution**.

Container names automatically resolve to container IP addresses.

---

# Task 6 – Putting Everything Together

### Create Network

```bash
docker network create app-network
```

### Create Volume

```bash
docker volume create db-data
```

### Run Database Container

```bash
docker run -d \
--name mysql-db \
--network app-network \
-e MYSQL_ROOT_PASSWORD=admin \
-v db-data:/var/lib/mysql \
mysql
```

### Run Application Container

```bash
docker run -it --network app-network ubuntu bash
```

### Test Connectivity

```bash
ping mysql-db
```

The application container successfully reaches the database using the **container name**.

---

# Key Learnings

### Docker Volumes

* Used for **persistent data storage**
* Data survives container deletion
* Best suited for databases

### Bind Mounts

* Connect container directories to host directories
* Useful during development
* Changes reflect instantly

### Docker Networking

* Default bridge network does not support name resolution
* Custom bridge networks enable container name communication

---

# Summary

Today’s tasks demonstrated how Docker handles:

1. **Persistent storage using Volumes**
2. **Live file access using Bind Mounts**
3. **Container communication using Networks**

These concepts are essential when building **multi-container applications and microservices architectures**.
