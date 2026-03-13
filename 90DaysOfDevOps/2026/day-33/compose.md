# Docker Compose: Multi-Container Basics

## What is Docker Compose?

Docker Compose is a tool that allows you to define and run multi-container applications using a single YAML file.

----
### Task 1: Install & Verify

<img width="491" height="113" alt="image" src="https://github.com/user-attachments/assets/d0b8bc14-3b68-4d31-9132-05a2af8e9bb6" />

```
apt update
apt get-install docker-compose-plugin
```

------

### Task 1: Install & Verify

```

```

------

### Task 2: Your First Compose File


```
Create a folder compose-basics
Write a docker-compose.yml that runs a single Nginx container with port mapping
Start it with docker compose up
Access it in your browser
Stop it with docker compose down
```

<img width="427" height="278" alt="image" src="https://github.com/user-attachments/assets/d384144f-54d7-4552-93bc-f606e15ec349" />

**Running on web**
<img width="1741" height="394" alt="image" src="https://github.com/user-attachments/assets/e6012552-ea73-41b0-a79d-23a166b366c0" />

<img width="720" height="307" alt="image" src="https://github.com/user-attachments/assets/634bb77f-97e7-473c-86ef-419e46dca7d9" />

------

### Task 3: Two-Container Setup

* Now we create a real application stack.

- WordPress requires:

    - MySQL database

    - Web container

Compose runs them together.

**Architecture**

```
Browser
   |
WordPress Container
   |
MySQL Container
   |
Volume (mysql-data)
```

**Explanation**
db service

```
db:
  image: mysql:5.7
```

Runs MySQL database container.

**environment variables**

```
environment:
  MYSQL_ROOT_PASSWORD: rootpass
```

Configures MySQL automatically.

**volume**
```
volumes:
  - mysql-data:/var/lib/mysql
```

This means:

named volume → mysql-data
container path → /var/lib/mysql

MySQL stores data here.

So even if container is removed:

data remains safe

**wordpress service**

```
wordpress:
  image: wordpress:latest
```

Runs WordPress application.

**DB connection**

```
WORDPRESS_DB_HOST: db
```

Important concept:

Compose automatically creates a network.

Services communicate using service names.

So:

wordpress → db

No IP required.

depends_on
depends_on:
  - db

Ensures:

MySQL starts before WordPress

**Step 2 — Start application**

```
docker compose up -d
```

**Step 3 — Check running containers**

```
docker ps
```

You should see:

wordpress-app
wordpress-db

**Step 4 — Open WordPress**

Browser:

http://localhost:8081

WordPress setup screen appears.

Set:

Site name

Admin user

Password

**Step 5 — Verify persistence**

Stop everything:

```
docker compose down
```

Start again:

```
docker compose up -d
```

Open WordPress again.

___________________________
*docker-compose.yml*

```
# create services for wordpress an dmysql
#
services:
  wordpress:
    image: wordpress:latest
    container_name: wordpress
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: root
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - mysql
    networks:
      - wp-network

  mysql:
    image: mysql:8.0
    container_name: mysql-service
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: root
    volumes:
      - mysql-data:/var/lib/mysql
    ports:
      - "3306:3306"
    restart: always
    networks:
      - wp-network


volumes:
  mysql-data:

networks:
  wp-network:
    driver: bridge

```

**IMPORTANT**

Simple rule

Variable	Must Match

WORDPRESS_DB_USER	MYSQL_USER

WORDPRESS_DB_PASSWORD	MYSQL_PASSWORD

WORDPRESS_DB_NAME	MYSQL_DATABASE

**Output**
<img width="764" height="359" alt="image" src="https://github.com/user-attachments/assets/191b0486-203f-423a-8b47-0631ce810fa4" />

<img width="752" height="414" alt="image" src="https://github.com/user-attachments/assets/e7864eef-d601-4449-a5c9-b38f739e47b9" />

_website:_

<img width="1904" height="981" alt="image" src="https://github.com/user-attachments/assets/dcdfb5eb-cc17-4e02-a261-a375b523c6c8" />


<img width="828" height="789" alt="image" src="https://github.com/user-attachments/assets/28d8a0c8-4e5e-4af5-9572-b6c420c1a4b8" />

------

### Task 4: Compose Commands

```
Start services in detached mode
View running services
View logs of all services
View logs of a specific service
Stop services without removing
Remove everything (containers, networks)
Rebuild images if you make a change

```

| Command                         | Purpose / Meaning                            |
| ------------------------------- | -------------------------------------------- |
| `docker compose up`             | Starts all services defined in compose file. |
| `docker compose up -d`          | Runs containers in background.               |
| `docker compose ps`             | Shows status of compose services.            |
| `docker compose logs`           | Shows logs for all services.                 |
| `docker compose logs -f`        | Streams logs in real-time.                   |
| `docker compose logs <service>` | Shows logs for a specific service.           |
| `docker compose stop`           | Stops containers without removing them.      |
| `docker compose restart`        | Restarts services.                           |
| `docker compose down`           | Stops and removes containers and networks.   |
| `docker compose down -v`        | Removes containers, networks, and volumes.   |
| `docker compose up --build`     | Rebuilds images before starting containers.  |


------

### Task 5: Environment Variables

Environment variables help store **configuration separately from code**.

---

## Using Variables in Compose File

```yaml
environment:
  MYSQL_ROOT_PASSWORD: rootpass
```

---

## Using `.env` File

Create a `.env` file:

```
MYSQL_ROOT_PASSWORD=rootpass
MYSQL_DATABASE=wordpress
MYSQL_USER=wpuser
MYSQL_PASSWORD=wppass
```

Update compose file:

```yaml
environment:
  MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
```

---

### Verify Variables Inside Container

| Command                                | Purpose / Meaning                                    |
| -------------------------------------- | ---------------------------------------------------- |
| `docker exec -it <container-name> env` | Displays environment variables inside the container. |

---

# Key Concepts Learned

| Concept               | Explanation                                                               |
| --------------------- | ------------------------------------------------------------------------- |
| Docker Compose        | Tool used to manage multi-container applications with a single YAML file. |
| Services              | Containers defined in the compose file.                                   |
| Networks              | Compose automatically creates a network for services to communicate.      |
| Volumes               | Used to persist data even if containers are removed.                      |
| Service Names         | Containers communicate using service names instead of IP addresses.       |
| Environment Variables | Used for secure and flexible configuration management.                    |

---

# Project Structure

```
compose-basics
│
├── docker-compose.yml
├── .env
└── day-33-compose.md
```

---

# Outcome

By completing this task I learned:

* How to run **multi-container applications using Docker Compose**
* How **containers communicate using service names**
* How to **persist data using Docker volumes**
* How to manage services using **Compose commands**
* How to configure applications using **environment variables**

Docker Compose is widely used in **development, testing environments, and microservice architectures** because it simplifies infrastructure setup and management
