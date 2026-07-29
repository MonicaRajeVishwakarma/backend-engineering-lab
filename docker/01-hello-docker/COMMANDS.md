# Docker Commands Cheat Sheet

## Verify Docker Installation

```bash
docker --version
```

Displays the installed Docker version.

---

## Display Docker Information

```bash
docker info
```

Shows information about the Docker Engine.

---

## Build a Docker Image

```bash
docker build -t hello .
```

### Explanation

- `docker build` → Build a Docker image.
- `-t hello` → Tag (name) the image as `hello`.
- `.` → Use the current directory as the build context.

---

## List Docker Images

```bash
docker images
```

Displays all images stored locally.

---

## Run a Container

```bash
docker run hello
```

Starts a container from the `hello` image.

---

## Run a Container with Port Mapping

```bash
docker run -p 8080:8080 hello
```

### Explanation

```
Host (Mac)          Container

localhost:8080 ---> :8080
```

The first port is the host port.

The second port is the container port.

---

## List Running Containers

```bash
docker ps
```

Shows currently running containers.

---

## List All Containers

```bash
docker ps -a
```

Shows both running and stopped containers.

---

## Stop a Running Container

```bash
docker stop <container-id>
```

Example:

```bash
docker stop abc123
```

---

## Remove a Container

```bash
docker rm <container-id>
```

Example:

```bash
docker rm abc123
```

---

## View Container Logs

```bash
docker logs <container-id>
```

Displays the logs produced by the application inside the container.

---

## Open a Shell Inside a Running Container

```bash
docker exec -it <container-id> sh
```

Example:

```bash
docker exec -it abc123 sh
```

Useful commands once inside:

```bash
pwd
ls
```

Exit the container:

```bash
exit
```

---

## Inspect an Image

```bash
docker image inspect hello
```

Displays detailed metadata about the image.

---

## Test the HTTP Server

Using curl:

```bash
curl localhost:8080
```

Or open in your browser:

```
http://localhost:8080
```

---

## Find Which Process Is Using Port 8080

```bash
lsof -i :8080
```

Example output:

```
COMMAND    PID
server     12345
```

Stop the process:

```bash
kill 12345
```

Force stop (if required):

```bash
kill -9 12345
```

---

# Docker Lifecycle

```
Go Code
    │
    ▼
Dockerfile
    │
docker build
    ▼
Docker Image
    │
docker run
    ▼
Running Container
```

---

# Image vs Container

**Image**

- Read-only blueprint
- Created using `docker build`
- Can create multiple containers

**Container**

- Running instance of an image
- Has its own filesystem and processes
- Can be started, stopped, and deleted

---

# Frequently Used Commands

```bash
docker build -t hello .
docker images
docker run -p 8080:8080 hello
docker ps
docker ps -a
docker stop <container-id>
docker rm <container-id>
docker logs <container-id>
docker exec -it <container-id> sh
docker image inspect hello
```