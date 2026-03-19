# 🐳 Docker Cheat Sheet

> Quick reference for everyday Docker usage. One line per command.

---

## 📦 Container Commands

| Command | What it does |
|---------|-------------|
| `docker run nginx` | Pull (if needed) and run a container |
| `docker run -it ubuntu bash` | Run interactively with a terminal |
| `docker run -d -p 8080:80 nginx` | Run detached, map host:container port |
| `docker run --name myapp -d nginx` | Run with a custom name |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (including stopped) |
| `docker stop <id/name>` | Gracefully stop a container |
| `docker kill <id/name>` | Force-kill a container immediately |
| `docker rm <id/name>` | Remove a stopped container |
| `docker rm -f <id/name>` | Force-remove a running container |
| `docker exec -it <id> bash` | Open a shell inside a running container |
| `docker logs <id>` | View container logs |
| `docker logs -f <id>` | Follow/stream container logs |
| `docker inspect <id>` | Full JSON details about a container |
| `docker stats` | Live resource usage (CPU, memory) |
| `docker cp file.txt <id>:/path` | Copy a file into a container |

---

## 🖼️ Image Commands

| Command | What it does |
|---------|-------------|
| `docker build -t myapp:1.0 .` | Build image from Dockerfile in current dir |
| `docker build -f Dockerfile.prod -t myapp .` | Build using a specific Dockerfile |
| `docker pull nginx:alpine` | Pull an image from Docker Hub |
| `docker push username/myapp:1.0` | Push image to Docker Hub |
| `docker tag myapp:1.0 username/myapp:1.0` | Tag an image for pushing |
| `docker images` | List local images |
| `docker image ls` | Same as above |
| `docker rmi <image>` | Remove an image |
| `docker image inspect <image>` | Full details about an image |
| `docker history <image>` | Show image layers and sizes |

---

## 💾 Volume Commands

| Command | What it does |
|---------|-------------|
| `docker volume create mydata` | Create a named volume |
| `docker volume ls` | List all volumes |
| `docker volume inspect mydata` | Show volume details and mount path |
| `docker volume rm mydata` | Remove a named volume |
| `docker run -v mydata:/app/data nginx` | Mount named volume into container |
| `docker run -v $(pwd):/app nginx` | Bind mount current directory |

---

## 🌐 Network Commands

| Command | What it does |
|---------|-------------|
| `docker network create mynet` | Create a custom bridge network |
| `docker network ls` | List all networks |
| `docker network inspect mynet` | Show network details and connected containers |
| `docker network connect mynet <container>` | Connect a running container to a network |
| `docker network disconnect mynet <container>` | Disconnect a container from a network |
| `docker network rm mynet` | Remove a network |
| `docker run --network mynet nginx` | Run container on a specific network |

---

## 🎼 Compose Commands

| Command | What it does |
|---------|-------------|
| `docker compose up` | Start all services (foreground) |
| `docker compose up -d` | Start all services (detached) |
| `docker compose up --build` | Rebuild images before starting |
| `docker compose down` | Stop and remove containers + networks |
| `docker compose down -v` | Same + delete named volumes |
| `docker compose ps` | List running Compose services |
| `docker compose logs -f` | Follow logs from all services |
| `docker compose logs web` | Logs from a specific service |
| `docker compose build` | Build/rebuild images only |
| `docker compose exec web bash` | Shell into a running service |
| `docker compose restart web` | Restart a specific service |
| `docker compose pull` | Pull latest images for all services |

---

## 🧹 Cleanup Commands

| Command | What it does |
|---------|-------------|
| `docker system df` | Show disk usage by images, containers, volumes |
| `docker system df -v` | Verbose breakdown per item |
| `docker system prune` | Remove all stopped containers, unused networks, dangling images |
| `docker system prune -a` | Also remove unused images (not just dangling) |
| `docker system prune -a --volumes` | Nuclear option — removes everything unused |
| `docker container prune` | Remove all stopped containers |
| `docker image prune` | Remove dangling images |
| `docker image prune -a` | Remove all unused images |
| `docker volume prune` | Remove all unused volumes |
| `docker network prune` | Remove all unused networks |
| `docker builder prune` | Clear build cache |

---

## 📄 Dockerfile Instructions

| Instruction | What it does |
|-------------|-------------|
| `FROM ubuntu:22.04` | Base image to build from |
| `WORKDIR /app` | Set working directory (creates if missing) |
| `COPY src/ ./src/` | Copy files from host into image |
| `ADD archive.tar.gz /app` | Like COPY but also extracts tar archives |
| `RUN apt-get install -y curl` | Execute command during build (creates a layer) |
| `ENV NODE_ENV=production` | Set environment variable in the image |
| `ARG VERSION=1.0` | Build-time variable (not in final image) |
| `EXPOSE 3000` | Document which port the app listens on |
| `VOLUME ["/data"]` | Declare a mount point |
| `CMD ["node", "app.js"]` | Default command to run (overridable) |
| `ENTRYPOINT ["python", "app.py"]` | Fixed executable (CMD becomes its args) |
| `USER node` | Switch to a non-root user |
| `LABEL maintainer="you@email.com"` | Add metadata to the image |
| `HEALTHCHECK CMD curl -f http://localhost/` | Define a container health check |
| `FROM builder AS final` | Multi-stage: name a stage and reference it |
| `COPY --from=builder /app/dist ./dist` | Copy from a previous build stage |

---

## ⚡ CMD vs ENTRYPOINT

```dockerfile
# CMD — default command, easily overridden at runtime
CMD ["node", "server.js"]
# docker run myapp python other.py  ← overrides CMD entirely

# ENTRYPOINT — locked-in executable, CMD becomes its default args
ENTRYPOINT ["python"]
CMD ["app.py"]
# docker run myapp other.py  ← runs: python other.py
```

**Rule of thumb:** Use `ENTRYPOINT` when the container IS a specific tool. Use `CMD` for default behavior you expect users to override.

---

## 🔧 docker-compose.yml Quick Reference

```yaml
version: "3.9"

services:
  web:
    build: .                          # build from local Dockerfile
    image: myapp:latest               # or use a pre-built image
    ports:
      - "8080:80"
    environment:
      - NODE_ENV=production
    env_file:
      - .env
    volumes:
      - ./src:/app/src                # bind mount
      - appdata:/app/data             # named volume
    networks:
      - backend
    depends_on:
      db:
        condition: service_healthy
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      retries: 3

  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: ${DB_PASS}
    volumes:
      - pgdata:/var/lib/postgresql/data
    networks:
      - backend
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s

volumes:
  appdata:
  pgdata:

networks:
  backend:
```

---

*Last updated: Day 37*
