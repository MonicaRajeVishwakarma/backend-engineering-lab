# Docker Networking

Docker networking allows containers to communicate with each other and with the outside world.

A real backend application usually contains multiple services:

```
                 User
                  |
                  |
             Load Balancer
                  |
                  |
          Go Backend Container
                  |
        --------------------
        |                  |
        ↓                  ↓
    PostgreSQL          Redis
    Container           Container
```

Docker networking enables these containers to discover and communicate with each other.

---

# Container Network Isolation

Every Docker container has its own:

- Filesystem
- Processes
- Network interface

A container behaves like a separate small computer.

Example:

```
Host Machine

IP: 192.168.1.10


Container

IP: 172.17.0.2
```

Containers do not automatically share the host network.

---

# localhost Inside Containers

The most important Docker networking rule:

> localhost always refers to the current machine or container.

Inside your Mac:

```
localhost
    |
    ↓
Your Mac
```

Inside a Go container:

```
localhost
    |
    ↓
Go container itself
```

Inside a PostgreSQL container:

```
localhost
    |
    ↓
PostgreSQL container itself
```

---

# Container Port vs Host Port

When running a container:

```bash
docker run -p 8080:8080 hello
```

The format is:

```
-p HOST_PORT:CONTAINER_PORT
```

Example:

```
Mac                     Container

localhost:8080  --->   :8080
```

The application inside the container listens on the container port.

Example Go server:

```go
func main() {
    http.ListenAndServe(":8080", nil)
}
```

The Go application does not know about the host port.

---

# Different Host and Container Ports

Host and container ports can be different.

Example:

```bash
docker run -p 9000:8080 hello
```

Means:

```
Mac                     Container

localhost:9000  --->   :8080
```

Inside the container:

```
Application listens on 8080
```

Outside:

```
User accesses localhost:9000
```

---

# EXPOSE Instruction

Example:

```dockerfile
EXPOSE 8080
```

EXPOSE tells Docker:

```
This application listens on port 8080
```

It is documentation metadata.

It does NOT:

- Open the port
- Publish the port
- Create a network
- Allow external access

---

# EXPOSE Example

Go application:

```go
func main() {
    http.ListenAndServe(":9090", nil)
}
```

Dockerfile:

```dockerfile
EXPOSE 9090
```

Run:

```bash
docker run -p 9090:9090 hello
```

Flow:

```
Mac

localhost:9090

      |
      ↓

Docker Port Mapping

      |
      ↓

Container

Go Server :9090
```

---

# Docker Networks

Containers communicate using Docker networks.

Create a network:

```bash
docker network create backend-network
```

List networks:

```bash
docker network ls
```

---

# Running Containers on a Network

Start PostgreSQL:

```bash
docker run \
--name postgres \
--network backend-network \
postgres
```

Start Go application:

```bash
docker run \
--name api \
--network backend-network \
hello
```

Now both containers are connected.

---

# Docker DNS

Docker provides automatic DNS inside networks.

Instead of:

```
localhost:5432
```

The Go application uses:

```
postgres:5432
```

because:

```
postgres

    |
    ↓

Docker DNS

    |
    ↓

PostgreSQL container IP
```

Container names become DNS names.

---

# Example Architecture

```
backend-network


+----------------+
| Go API         |
| api            |
+----------------+
        |
        |
        ↓
   postgres:5432
        |
        |
+----------------+
| PostgreSQL     |
| postgres       |
+----------------+
```

Application configuration:

```
DATABASE_HOST=postgres
DATABASE_PORT=5432
```

Not:

```
DATABASE_HOST=localhost
```

---

# Docker Network Types

View networks:

```bash
docker network ls
```

Example:

```
NETWORK ID     NAME       DRIVER

abc123         bridge     bridge
xyz456         host       host
def789         none       null
```

---

# Bridge Network

Default Docker networking mode.

Used for:

- Local development
- Docker Compose
- Most applications

Example:

```
Container A

      |
      |
Docker Bridge Network

      |
      |

Container B
```

---

# Host Network

Container shares the host machine network.

Example:

```
Mac Network

      |
      |
Container
```

Less isolation.

---

# None Network

Container has no network access.

Used for special cases.

---

# Foreground vs Detached Containers

## Foreground

```bash
docker run hello
```

Terminal attaches to container.

```
Terminal
   |
   |
Container Process
```

---

## Detached

```bash
docker run -d hello
```

Container runs in background.

Check:

```bash
docker ps
```

---

# Why Alpine Container Stops Immediately

Command:

```bash
docker run -d alpine
```

may stop immediately.

Reason:

A container runs as long as its main process runs.

Alpine starts:

```
/bin/sh
```

Without an attached terminal:

```
Shell exits

↓

Container stops
```

Check:

```bash
docker ps -a
```

---

# Keeping a Container Running

Example:

```bash
docker run -d \
--name client \
--network backend-network \
alpine sleep infinity
```

The process:

```
sleep infinity
```

keeps the container alive.

---

# Important Docker Networking Rules

## Rule 1

Containers are isolated.

```
Container A != Container B
```

---

## Rule 2

localhost means "myself".

```
localhost inside container
=
that container
```

---

## Rule 3

Containers communicate using Docker networks.

Use:

```
container-name:port
```

Example:

```
postgres:5432
```

---

# Key Takeaways

- Containers have their own network namespace.
- localhost does not mean the host machine.
- `-p HOST:CONTAINER` publishes ports.
- EXPOSE only documents ports.
- Containers on the same network communicate using names.
- Docker provides internal DNS.
- Docker networking is the foundation for Docker Compose and Kubernetes.

---

# Next Topics

1. Docker Volumes
2. Docker Compose
3. Running Databases with Containers
4. Containerizing Real Backend Applications
5. Kubernetes Networking