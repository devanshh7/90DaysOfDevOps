# Multi-Stage Builds & Docker Hub

**Multi-Stage Builds & Docker Hub**

Learn how to:

* Reduce Docker image size
* Build optimized images using multi-stage builds
* Push and distribute images through Docker Hub
*  Follow Docker image best practices

----------------

## Task 1 — The Problem with Large Images

Objective

Understand why normal **Docker images become large.**

When we build apps normally, the image contains:

* build tools
* compilers
* dependencies
* source code
* runtime

All of these increase **image size.**

### Step 1 — Create a Simple Node.js App

- Create folder:

```
mkdir node-app
cd node-app
```

- Create file:

app.js

```
const http = require("http");

const server = http.createServer((req,res)=>{
    res.end("Hello from Docker Multi Stage!");
});

server.listen(3000);
```

### Step 2 — Create a Single-Stage Dockerfile

```
FROM node:18

WORKDIR /app

COPY . .

RUN npm install

CMD ["node","app.js"]
```

### Step 3 — Build Image

`docker build -t node-single .`

### Step 4 — Check Image Size

`docker images`

Example output:

`node-single    950MB`

**Large size happens because:**

* Node image

* build tools

* npm cache

* development files

**Why This Is Bad**

Large images cause:

* slow deployments

* slow CI/CD pipelines

* higher storage usage

* larger security attack surface

-------------------------

## Step 2 — Create a Single-Stage Dockerfile
