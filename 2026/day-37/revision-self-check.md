# Day 37 – Docker Revision & Self-Check

> **Goal:** Consolidate everything from Days 29–36 so Docker actually sticks.

---

## ✅ Self-Assessment Checklist

Rate yourself honestly: `✅ Can do` | `🟡 Shaky` | `❌ Haven't done`

| Topic | Status | Notes |
|-------|--------|-------|
| Run a container from Docker Hub (interactive + detached) | ✅ Can do | `docker run nginx & docker run -d nginx`|
| List, stop, remove containers and images | ✅ Can do |`docker sps`, `docker stop containe-name` , `docker rm cont-name`, `docker rmi img-name/id` |
| Explain image layers and how caching works | 🟡 | |
| Write a Dockerfile from scratch (FROM, RUN, COPY, WORKDIR, CMD) | 🟡 |✅ Can do |
| Explain CMD vs ENTRYPOINT | 🟡 | |
| Build and tag a custom image | ✅ Can do | `docker build -t imge-name .` , `docker build -t img-name:tag(v1)`  |
| Create and use named volumes | ✅ Can do |`docker volume create my-data`, `docker run -d -p 8080:80 --name my-container -v my-data:/usr/share/nginx/html nginx:alpine
` |
| Use bind mounts | 🟡 Shaky | `docker run -it --mount type=bind,src=.,target=/src ubuntu bash` , >>>>>>_________ :    _`The --mount type=bind option tells Docker to create a bind mount, where src is the current working directory on your host machine (getting-started-app), and target is where that directory should appear inside the container (/src).`_|
| Create custom networks and connect containers | ✅ Can do |`docker network create <name-nw>`, I already running containe : `docker network connect app-net container1`, Else: `docker run -d --name container1 --network app-net nginx:alpine` |
| Write a docker-compose.yml for a multi-container app | 🟡 |Can do |
| Use environment variables and .env files in Compose | 🟡 |Can do |
| Write a multi-stage Dockerfile | 🟡 |Little Shaky.. |
| Push an image to Docker Hub | 🟡 |Can do |
| Use healthchecks and depends_on | 🟡 |yeah can do  |

> **How to update:** Replace `🟡` with `✅` once confident, or `❌` if you haven't tried it yet. Add personal notes in the Notes column.

---

## 🔥 Quick-Fire Questions

> Answer from memory first, then verify. Write your answers below each question.

---

### 1. What is the difference between an image and a container?

**My Answer:**

> **Docker img** is a blueprint (consists of : all the files,lib, binaries of appcn) to run o execute a container.Series of read-only layers where each layer responsible to performing instruction. Where as container is a running instance of that image and from one image we can build multiple containers. 

---

### 2. What happens to data inside a container when you remove it?

**My Answer:**

> It is **permanently lost**. Containers have a writable layer that is tied to their lifecycle. When you `docker rm` a container, that layer is deleted. To persist data, you must use **volumes** or **bind mounts** which live outside the container's lifecycle.

---

### 3. How do two containers on the same custom network communicate?

**My Answer:**

> Containers on the same custom bridge network can reach each other using their **container name** as the hostname. Docker's embedded DNS resolves names automatically. Example: a web container can connect to `db:5432` if the database container is named `db` on the same network.

---

### 4. What does `docker compose down -v` do differently from `docker compose down`?

**MY Answer:**

> `docker compose down` stops and removes containers and networks created by Compose. `docker compose down -v` does all of that **plus deletes the named volumes** declared in the `volumes:` section of your compose file. Use `-v` when you want a clean slate (e.g., wiping a database volume).

---

### 5. Why are multi-stage builds useful?

**My Answer:**

> Multi-stage builds let you **separate build-time dependencies from runtime**. For example, you compile code in a heavy `builder` stage (with compilers, dev tools), then copy only the compiled binary into a minimal final image. Result: much smaller, more secure production images with no build tools exposed.

---

### 6. What is the difference between `COPY` and `ADD`?

**My Answer:**

> `COPY` simply copies files/directories from the host into the image — straightforward and predictable. `ADD` does everything `COPY` does, but also **auto-extracts `.tar` archives** and can fetch files from **remote URLs**. Best practice: use `COPY` by default; only use `ADD` when you specifically need its extra features.

---

### 7. What does `-p 8080:80` mean?

**My Answer:**

> It maps **port 8080 on the host** to **port 80 inside the container**. Format is `HOST_PORT:CONTAINER_PORT`. So a request to `localhost:8080` on your machine is forwarded to whatever is listening on port 80 inside the container.

---

### 8. How do you check how much disk space Docker is using?

**My Answer:**

> Run `docker system df` — it shows a breakdown of disk usage by images, containers, volumes, and build cache, including reclaimable space. Add `-v` for a verbose per-item breakdown.

---

## 📝 Personal Notes & Gaps

> Use this section to note anything you found confusing or want to revisit.

```
If container removed --> Data removed unless you used Volumes..

To see docker usage --> docker system df -v(for per item breakdown)
```

---

## 🔗 Reference

- Cheat sheet: [`docker-cheatsheet.md`](./docker-cheatsheet.md)
- Docker Docs: https://docs.docker.com
