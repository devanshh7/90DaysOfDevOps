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
docker run --name postgres-test \
-e POSTGRES_PASSWORD=admin \
-d postgres
```

### Enter the Container

```bash
docker exec -it postgres-test psql -U postgres
```

### Create Sample Data

```sql
CREATE TABLE students (
id SERIAL PRIMARY KEY,
name TEXT
);

INSERT INTO students (name) VALUES ('Devansh'), ('Docker');
SELECT * FROM students;
```

### Stop and Remove Container

```bash
docker stop postgres-test
docker rm postgres-test
```

### Run a New Container

```bash
docker run --name postgres-test \
-e POSTGRES_PASSWORD=admin \
-d postgres
```

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
CREATE TABLE test_data (name TEXT);
INSERT INTO test_data VALUES ('Docker Volume');
SELECT * FROM test_data;
```

### Remove Container

```bash
docker stop postgres-vol
docker rm postgres-vol
```

### Run New Container Using Same Volume

```bash
docker run --name postgres-vol2 \
-e POSTGRES_PASSWORD=admin \
-v postgres-data:/var/lib/postgresql/data \
-d postgres
```

### Result

The data **still exists** because it is stored in the **Docker volume**.

### Verify Volume

```bash
docker volume ls
```

```bash
docker volume inspect postgres-data
```

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
