# Docker Project: Dockerize a Full Application

**Task**

**Today's goal is to take a real application and Dockerize it end-to-end.**

**No tutorials. No hand-holding. Pick an app, write the Dockerfile, set up Compose, and ship it. This is what you'll do on the job.**

---------

## What are we going to do??????

We’ll build a **complete production-style project from scratch like a full-stack + DevOps engineer.**

This will include:

* **Frontend** → simple UI (served by Nginx)

* **Backend API** → Node.js + Express

* **Database** → MongoDB

* **Reverse Proxy** → Nginx

* **Containers** → Docker

* **Orchestration** → Docker Compose

By the end you will have a **real full-stack Dockerized application** you can push to GitHub and Docker Hub.

----------------------------

### Project Architecture

![alt text](images/image.png)

-------------

## STEP 1 — Create Project Folder

- Create the project directory.

```
mkdir docker-fullstack-app
cd docker-fullstack-app
```

- Create folders.
  
```
mkdir backend
mkdir nginx
mkdir frontend
```

- Project Structure:
  
```
docker-fullstack-app
│
├── backend
├── frontend
├── nginx
├── docker-compose.yml
├── .env
└── README.md
```
## STEP 2 — Build Backend (Node.js API)

![alt text](images/image-1.png)

**created backend API**

![alt text](images/image-2.png)


## STEP 3 — Backend Dockerfile

- created /backend/dockerfile

```
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

EXPOSE 5000

CMD ["node","app.js"]
```

![alt text](images/image-4.png)

## STEP 4 — Create Frontend

- Created html file in /frontend/index.htm

```
<!DOCTYPE html>
<html>
<head>
<title>Docker Fullstack App</title>
</head>

<body>

<h1>User Manager</h1>

<input id="username" placeholder="Enter name"/>
<button onclick="addUser()">Add User</button>

<ul id="users"></ul>

<script>

async function loadUsers(){
    const res = await fetch("/api/users");
    const users = await res.json();

    const list = document.getElementById("users");
    list.innerHTML="";

    users.forEach(u=>{
        const li=document.createElement("li");
        li.innerText=u.name;
        list.appendChild(li);
    });
}

async function addUser(){
    const name=document.getElementById("username").value;

    await fetch("/api/users",{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({name})
    });

    loadUsers();
}

loadUsers();

</script>

</body>
</html>
```

![alt text](images/image-3.png)

- Frontend will call backend via:
- `/api/users`
  

## STEP 5 — Configure Nginx

- create /nginx.nginx.conf
  
```
events {}

http {

server {

listen 80;

location / {
root /usr/share/nginx/html;
index index.html;
}

location /api/ {
proxy_pass http://backend:5000/;
}

}

}
```
  
  ![alt text](images/image-5.png)

* **Explanation:**

```
/ → serve frontend
/api → forward request to backend container

```

## STEP 6 — Docker Compose

- create dockercompose inside /full-stack folser:

```
version: "3.8"

services:

  mongo:
    image: mongo:6
    container_name: mongo_db
    volumes:
      - mongo-data:/data/db
    networks:
      - app-network

  backend:
    build: ./backend
    container_name: node_backend
    environment:
      - MONGO_URI=mongodb://mongo:27017/mydb
    depends_on:
      - mongo
    networks:
      - app-network

  nginx:
    image: nginx:alpine
    container_name: nginx_server
    ports:
      - "80:80"
    volumes:
      - ./frontend:/usr/share/nginx/html
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - backend
    networks:
      - app-network

networks:
  app-network:

volumes:
  mongo-data:
```
![alt text](images/image-6.png)

## STEP 7 — Environment Variables

- Create .env
  
  `MONGO_URI=mongodb://mongo:27017/mydb`

## STEP 8 — Docker Ignore

- create `.dockerignore`

```
node_modules
.git
.env
```
![alt text](images/image-7.png)

## STEP 9 — Run the Project

- From root folder:

```
docker compose up --build
```
- Docker will start:

```
mongo_db
node_backend
nginx_server
```
![alt text](images/image-8.png)


### Docker compose up --build

## STEP 10 — Test the Application
1.

![alt text](images/image-10.png)

2.

![alt text](images/image-9.png)

3.

![alt text](images/image-11.png)

4. On `/api/users` user shows:
   
   ![alt text](images/image-12.png)

------

## STEP 11 — Push Backend Image to Docker Hub

- Login:
 
 `docker login`

- BUild Image: 

`docker build -t yourusername/docker-backend ./backend`

<img width="887" height="382" alt="image" src="https://github.com/user-attachments/assets/1efb17de-c1f9-4ea0-a2da-8673b87be312" />

- Tag image:

`docker tag yourusername/docker-backend yourusername/docker-backend:v1`

- Push Image:

`docker push yourusername/docker-backend:v1`

<img width="1087" height="268" alt="image" src="https://github.com/user-attachments/assets/8cadc9da-8f1a-4647-9595-fe8ce1274d6f" />

**Updated Docker-compose.yml**

<img width="754" height="637" alt="image" src="https://github.com/user-attachments/assets/2d478381-b938-4538-9b05-a9a9d50bf2eb" />

