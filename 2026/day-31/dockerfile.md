# Dockerfile Practice Progress Tracker

# Section 1 — Dockerfile Basics

## Task 1 — Your First Dockerfile

**A Dockerfile is a text file that contains instructions used by Docker to automatically build a Docker image**

Goal: Build a custom image using Ubuntu and print a message.

### Steps

* [x] Create project folder `my-first-image`
* [ ] Create a `Dockerfile`
* [ ] Use `ubuntu` as base image
* [ ] Install `curl`
* [ ] Add default command printing message
* [ ] Build image `my-ubuntu:v1`
* [ ] Run container from image
* [ ] Verify message prints

### Example Dockerfile

```
FROM ubuntu:latest

RUN apt-get update && apt-get install -y curl

CMD ["echo", "Hello from my custom image!"]
```

### Build Command

```
docker build -t my-ubuntu:v1 .
```

### Run Command

```
docker run my-ubuntu:v1
```

### Output

Expected:

```
Hello from my custom image!
```
<img width="1037" height="357" alt="image" src="https://github.com/user-attachments/assets/722275ed-a7ce-48a4-800f-bdc900c607ab" />

<img width="774" height="274" alt="image" src="https://github.com/user-attachments/assets/85a42655-913b-4678-b3fd-0908ab13e373" />

### Notes

```
Add your observations here.
```

---

# Task 2 — Dockerfile Instructions

Goal: Practice common Dockerfile instructions.

### Instructions to Use

* FROM
* RUN
* COPY
* WORKDIR
* EXPOSE
* CMD

### Example Project Structure

```
project/
 ├── Dockerfile
 └── app.txt
```

### Example Dockerfile

```
FROM ubuntu:latest

RUN apt-get update && apt-get install -y curl

WORKDIR /app

COPY app.txt .

EXPOSE 8080

CMD ["cat", "app.txt"]
```

### Steps

* [ ] Create Dockerfile
* [ ] Add test file (`app.txt`)
* [ ] Build image
* [ ] Run container

### Build

```
docker build -t dockerfile-demo:v1 .
```

### Run

```
docker run dockerfile-demo:v1
```

### What Each Instruction Does

| Instruction | Purpose                       |
| ----------- | ----------------------------- |
| FROM        | Base image                    |
| RUN         | Execute commands during build |
| COPY        | Copy files into image         |
| WORKDIR     | Set working directory         |
| EXPOSE      | Document port                 |
| CMD         | Default runtime command       |

<img width="768" height="205" alt="image" src="https://github.com/user-attachments/assets/c40cc529-1762-443d-b68f-cc3eeabb0db7" />

d<img width="941" height="280" alt="image" src="https://github.com/user-attachments/assets/56bc7372-e78d-44cc-bc32-267bcb558ab9" />

### Notes

```
Your explanation of each instruction
```

---

# Task 3 — CMD vs ENTRYPOINT

## Part 1 — CMD

Dockerfile:

```
FROM ubuntu

CMD ["echo", "hello"]
```

Run:

```
docker run cmd-test
```

Override command:

```
docker run cmd-test ls
```

### Observation

```
Write what happens when CMD is overridden
```

---

## Part 2 — ENTRYPOINT

Dockerfile:

```
FROM ubuntu

ENTRYPOINT ["echo"]
```

Run:

```
docker run entrypoint-test hello
```

Run with extra argument:

```
docker run entrypoint-test world
```

### Observation

```
Write what happens when arguments are added
```

---

### CMD vs ENTRYPOINT Notes

```
When to use CMD:

When to use ENTRYPOINT:
```

---

# Task 4 — Build a Simple Web App Image

Goal: Serve a static webpage.

### Project Structure

```
website/
 ├── Dockerfile
 └── index.html
```

### Example HTML

```
<h1>Hello from my Docker website</h1>
<p>This page is served by Nginx inside a container.</p>
```

### Dockerfile

```
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

### Build

```
docker build -t my-website:v1 .
```

### Run

```
docker run -d -p 8080:80 my-website:v1
```

### Test

Open browser:

```
http://localhost:8080
```

### Notes

```
Add screenshots or observations
```

---

# Task 5 — .dockerignore

Create a `.dockerignore` file.

### Example

```
node_modules
.git
*.md
.env
```

### Steps

* [ ] Create `.dockerignore`
* [ ] Add entries
* [ ] Rebuild image
* [ ] Verify ignored files not included

### Notes

```
What files were excluded?
```

---

# Task 6 — Build Optimization

Goal: Understand Docker build cache.

### Experiment

1. Build image
2. Change a line
3. Rebuild image

### Observe

```
Which layers rebuild?
Which layers use cache?
```

---

### Optimized Dockerfile Example

Bad order:

```
COPY . .
RUN npm install
```

Better order:

```
COPY package.json .
RUN npm install
COPY . .
```

### Why Layer Order Matters

```
Write your explanation here
```

---

# Final Progress Checklist

* [ ] First Docker image built
* [ ] Practiced Dockerfile instructions
* [ ] Understood CMD vs ENTRYPOINT
* [ ] Built web server image
* [ ] Used .dockerignore
* [ ] Tested build cache optimization

---

# Extra Notes

```
Add anything useful you learned during the exercises.
```
