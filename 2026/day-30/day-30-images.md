# Docker Images & Container Lifecycle

**Pull Images**
```
docker pull nginx/ubuntu/alpine
```

**List Images**
`docker images`

**Compare ubuntu vs alpine — why is one much smaller?**

Alpine image is smaller as compared to ubuntu.

Key reasons

1️⃣ musl instead of glibc

* Alpine uses musl libc, a lightweight C standard library.

* glibc (used by Ubuntu) is bigger but more compatible.

2️⃣ BusyBox replaces many tools

* Alpine bundles many commands (ls, cp, cat, etc.) into one binary using BusyBox.

* Ubuntu installs separate GNU utilities.

3️⃣ Minimal base system

* Alpine installs only the essentials.

* Ubuntu includes many standard libraries and utilities by default.

4️⃣ Designed for containers

* Alpine was intentionally designed to be tiny and secure.


