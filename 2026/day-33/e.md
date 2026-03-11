# Day 33 – Docker Compose: Multi-Container Basics

## Overview

Today's goal was to understand how **Docker Compose simplifies running multi-container applications**.
Instead of manually creating containers, networks, and volumes, Docker Compose allows defining everything inside a **single YAML configuration file** and running it with one command.

---

# Task 1 – Install & Verify Docker Compose

| Command                  | Purpose / Meaning                                                              |
| ------------------------ | ------------------------------------------------------------------------------ |
| `docker compose version` | Checks whether Docker Compose is installed and displays the installed version. |
| `docker --version`       | Confirms Docker Engine is installed and working.                               |

---

# Task 2 – Run First Compose Application (Nginx)

### Project Setup

| Command                    | Purpose / Meaning                                             |
| -------------------------- | ------------------------------------------------------------- |
| `mkdir compose-basics`     | Creates a new project directory for the Docker Compose setup. |
| `cd compose-basics`        | Moves into the project directory.                             |
| `touch docker-compose.yml` | Creates the Compose configuration file.                       |

---

### Basic docker-compose.yml

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:latest
    ports:
      - "8080:80"
```

---

### Running the Application

| Command                | Purpose / Meaning                                             |
| ---------------------- | ------------------------------------------------------------- |
| `docker compose up`    | Starts services defined in the compose file.                  |
| `docker compose up -d` | Starts services in detached mode (background).                |
| `docker compose ps`    | Lists running services managed by Docker Compose.             |
| `docker compose down`  | Stops and removes containers and networks created by Compose. |

---

### Access Application

Open in browser:

```
http://localhost:8080
```

This loads the **default Nginx page**.

---

# Task 3 – Multi-Container Setup (WordPress + MySQL)

This task demonstrates how **multiple containers communicate with each other using Docker Compose networking**.

### docker-compose.yml

```yaml
version: '3.8'

services:

  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - mysql-data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8081:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db

volumes:
  mysql-data:
```

---

### Start the Stack

| Command                | Purpose / Meaning                                       |
| ---------------------- | ------------------------------------------------------- |
| `docker compose up -d` | Starts WordPress and MySQL containers in detached mode. |
| `docker ps`            | Lists running containers.                               |
| `docker compose down`  | Stops and removes containers but keeps volumes.         |

---

### Access WordPress

Open browser:

```
http://localhost:8081
```

Complete the WordPress installation wizard.

---

### Data Persistence Test

| Command                | Purpose / Meaning     |
| ---------------------- | --------------------- |
| `docker compose down`  | Stops the containers. |
| `docker compose up -d` | Starts them again.    |

If your **WordPress site remains configured**, it means the **MySQL volume persisted the data**.

---

# Task 4 – Important Docker Compose Commands

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

---

# Task 5 – Environment Variables

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

Docker Compose is widely used in **development, testing environments, and microservice architectures** because it simplifies infrastructure setup and management.
