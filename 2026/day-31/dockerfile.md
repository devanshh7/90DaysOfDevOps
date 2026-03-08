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
FROM

Defines the base image.
This tells Docker to start from the Ubuntu image.

RUN

Executes commands during image build.

COPY

Copies files from host machine → Docker image.

WORKDIR

Sets the working directory inside the container.

Example:

WORKDIR /app

All subsequent commands run from this directory.


EXPOSE

Documents which port the container will use.

Example:

EXPOSE 5000

This does not publish the port, but informs users.

CMD

Defines the default command executed when the container starts.

Example:

CMD ["python3", "-m", "http.server", "5000"]

This runs a simple Python web server.

docker ps
<img width="901" height="569" alt="image" src="https://github.com/user-attachments/assets/ebfbbf93-97c2-4563-87f8-5d38bccdc4c3" />


<img width="905" height="273" alt="image" src="https://github.com/user-attachments/assets/c77376e9-acdd-4919-bb29-153686d7adba" />

<img width="1905" height="627" alt="image" src="https://github.com/user-attachments/assets/9b88e3c5-0b90-4967-9f61-4db26e2d7242" />

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
<img width="882" height="403" alt="image" src="https://github.com/user-attachments/assets/76651d24-1924-4bf4-ae6c-c05f8b87bf2b" />

Override command:

```
docker run cmd-test ls
```

```
docker run cmd-test echo hii
```
<img width="782" height="43" alt="image" src="https://github.com/user-attachments/assets/6488d66d-a2ec-48ca-8c85-445d924b9c7d" />

### Observation

```
Write what happens when CMD is overridden
```

Firstly when we make dockerfile and run it <FROM ubuntu
CMD ["echo", "hello"]> it runs and prints hello but when we give custom command like <docker run test-cmd echo hiii> then it prints hii instead of hello.

---

## Part 2 — ENTRYPOINT

Dockerfile:

```
FROM ubuntu

ENTRYPOINT ["echo"]
```
<img width="307" height="173" alt="image" src="https://github.com/user-attachments/assets/d8f1e3a4-477f-4faa-ba78-a92fc3b979d2" />

Run:

Without argument:
<img width="1055" height="374" alt="image" src="https://github.com/user-attachments/assets/9cf13b02-f9a7-4141-a668-888f1b398490" />


```
docker run entrypoint-test hello
```

Run with extra argument:

```
docker run entrypoint-test world
```
<img width="840" height="90" alt="image" src="https://github.com/user-attachments/assets/9c1f4856-24f3-44e3-940b-b8d6e6873ebd" />


### Observation

```
Write what happens when arguments are added
```
When arguments are given the echo command is running else it is printing blank.

---

### CMD vs ENTRYPOINT Notes

```
When to use CMD:

When to use ENTRYPOINT:
```

ENTRYPOINT and CMD are similar but separate instructions that complement each other:

ENTRYPOINT is the process executed inside the container.
CMD is the default set of arguments supplied to the ENTRYPOINT process.
There are also differences in how you override these values when you start a container:

CMD is easily overridden by appending your own arguments to the docker run command.
ENTRYPOINT can be changed using the --entrypoint flag. However, this should rarely be necessary for container images if they are used in the way intended. If you do change the ENTRYPOINT, you’ll almost certainly need to set a custom CMD too. Otherwise, your new ENTRYPOINT is likely to receive arguments it doesn’t understand.

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
<img width="727" height="123" alt="image" src="https://github.com/user-attachments/assets/aba4427f-2750-4fde-aae4-033f0152b611" />


### Build

```
docker build -t my-website:v1 .
```
<img width="836" height="419" alt="image" src="https://github.com/user-attachments/assets/19c02d0d-119d-447e-9c99-0789e2f0ffde" />

### Run

```
docker run -d -p 8080:80 my-website:v1
```

### Test

Open browser:

```
http://localhost:8080
```
<img width="1919" height="1005" alt="image" src="https://github.com/user-attachments/assets/fcb7c3e4-9046-42fc-8f80-7782605fcc7e" />

### Notes

```
Add screenshots or observations
```
<img width="949" height="875" alt="image" src="https://github.com/user-attachments/assets/fb541f25-d1a1-4b99-8fcd-74f9f426ace0" />

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
