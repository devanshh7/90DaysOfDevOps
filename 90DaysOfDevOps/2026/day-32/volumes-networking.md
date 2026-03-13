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
docker stop mysql-vol2
docker rm mysql-vol2
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
-p 8000:80 \
-v $(pwd):/usr/share/nginx/html \
--name nginx-bind \
nginx
```

Open browser:

```
http://localhost:8080
```
<img width="1900" height="595" alt="image" src="https://github.com/user-attachments/assets/a356bb20-26b2-4be6-bbaa-6f46b791a857" />

Edit `index.html` and refresh the page — changes appear instantly.

<img width="758" height="353" alt="image" src="https://github.com/user-attachments/assets/32c0cc3c-0564-4f39-8e12-216870bd2d73" />

<img width="1802" height="503" alt="image" src="https://github.com/user-attachments/assets/4722ca8f-c388-4e9e-9cb6-5cd49e5d7504" />

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
<img width="618" height="121" alt="image" src="https://github.com/user-attachments/assets/a3972eda-553c-4d79-802a-c15e27c415d9" />

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
<img width="831" height="413" alt="image" src="https://github.com/user-attachments/assets/b79562db-6270-4d8d-827d-18c07afbc547" />

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
<img width="760" height="465" alt="image" src="https://github.com/user-attachments/assets/a0ef0c26-1fb4-4913-986a-52738d7be192" />

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
<img width="947" height="229" alt="image" src="https://github.com/user-attachments/assets/89a5c41e-bd2e-46f1-a8f5-8f3c0451e1db" />

### Test Communication

```bash
docker exec -it app1 ping app2
```
<img width="724" height="340" alt="image" src="https://github.com/user-attachments/assets/a5509c0a-707e-4e83-a62c-9b6ae0159cd2" />

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
<img width="660" height="47" alt="image" src="https://github.com/user-attachments/assets/737453b3-bc8a-47df-b4fa-d1e3cb673dcd" />

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
<img width="932" height="522" alt="image" src="https://github.com/user-attachments/assets/95998707-6d2f-4b16-8c9e-9ab34ed3776c" />

### Run Application Container

Why Another Container Is Needed

To test Docker networking we need two containers.

- Think of it like this:

`Application → Database`

- Example in real systems:

`Backend API → MySQL database`

- So we create another container that represents an application container.

`app-client`

```bash
docker run -it \
--name app-client \
--network devops-net \
ubuntu bash
```

<img width="621" height="517" alt="image" src="https://github.com/user-attachments/assets/a0e9485c-e20d-4d81-8589-87ec589ffa32" />

### Test Connectivity

```bash
ping mysql-db
```
<img width="822" height="287" alt="image" src="https://github.com/user-attachments/assets/7b9ea057-6360-4416-a110-f6dc0d6ab3ac" />

Inside `devops-net` network we can see inside container : "mysql-server" is there inside the network.

<img width="976" height="817" alt="image" src="https://github.com/user-attachments/assets/125daf38-f8fe-4b09-8e0e-3493a500f0fd" />

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
