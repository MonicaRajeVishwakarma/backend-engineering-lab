# Docker Volumes

## Overview

Docker volumes provide **persistent storage** for containers.

By default, a container's filesystem is **ephemeral**, meaning any data stored inside the container is lost when the container is removed.

Volumes solve this problem by storing data outside the container's writable layer.

---

# Why Do We Need Volumes?

Consider a PostgreSQL container.

```
+--------------------------+
| PostgreSQL Container     |
|                          |
|  /var/lib/postgresql     |
|      └── data            |
|           ├── users      |
|           ├── orders     |
|           └── products   |
+--------------------------+
```

If the container is removed:

```bash
docker rm postgres
```

Everything inside the container's filesystem is deleted.

```
Container Removed
        │
        ▼
Filesystem Deleted
        │
        ▼
Database Lost ❌
```

---

# Solution: Docker Volumes

A Docker volume exists independently of containers.

```
            +----------------------+
            | PostgreSQL Container |
            +----------+-----------+
                       |
                       |
                       ▼
             +-------------------+
             | Docker Volume     |
             | postgres-data     |
             +-------------------+
```

The container can be deleted, but the volume remains.

---

# Container vs Volume

| Container | Volume |
|-----------|--------|
| Runs the application | Stores persistent data |
| Temporary | Persistent |
| Can be recreated anytime | Should survive container recreation |
| Built from an image | Managed by Docker |

---

# Creating a Volume

Create a new volume:

```bash
docker volume create postgres-data
```

List all volumes:

```bash
docker volume ls
```

Example output:

```
DRIVER    VOLUME NAME

local     postgres-data
```

---

# Using a Volume

Run PostgreSQL with a volume:

```bash
docker run \
--name postgres \
-e POSTGRES_PASSWORD=password \
-v postgres-data:/var/lib/postgresql/data \
postgres
```

The syntax is:

```bash
-v VOLUME_NAME:CONTAINER_PATH
```

Example:

```
Docker Volume

postgres-data

        │
        ▼

/var/lib/postgresql/data

inside the container
```

---

# Data Persistence

Imagine the following sequence:

```
Start PostgreSQL
        │
        ▼

Insert Data
        │
        ▼

Stop Container
        │
        ▼

Remove Container
        │
        ▼

Create New Container
        │
        ▼

Attach Same Volume
        │
        ▼

Data Still Exists ✅
```

The application container changed.

The volume did not.

---

# Real Example

Start PostgreSQL:

```bash
docker run \
--name postgres \
-e POSTGRES_PASSWORD=password \
-v postgres-data:/var/lib/postgresql/data \
postgres
```

Create a database:

```sql
CREATE DATABASE ecommerce;
```

Remove the container:

```bash
docker stop postgres
docker rm postgres
```

Create another PostgreSQL container using the same volume:

```bash
docker run \
--name postgres-new \
-e POSTGRES_PASSWORD=password \
-v postgres-data:/var/lib/postgresql/data \
postgres
```

The `ecommerce` database still exists because it is stored in the volume.

---

# How Containers Attach to Volumes

A container mounts the volume into one of its directories.

```
Container

/app/data

      │
      ▼

Docker Volume

my-data
```

The application reads and writes files as if they were inside `/app/data`.

Docker transparently stores those files in the volume.

---

# Can Multiple Containers Use the Same Volume?

Yes.

```
          shared-volume

          ▲         ▲
          │         │
          │         │

+---------+--+   +--+---------+
| Container A |   | Container B |
+------------+   +-------------+
```

Example:

```bash
docker run -v shared-data:/data container-a

docker run -v shared-data:/data container-b
```

Both containers see the same files under `/data`.

---

# Should Multiple Containers Share a Volume?

It depends.

### Good Use Cases

- Shared application assets
- Log processing
- Configuration files
- Development environments

### Use With Care

Databases generally expect exclusive access to their data directory.

Running two PostgreSQL containers against the same data directory can corrupt the database.

---

# Types of Docker Storage

## 1. Docker Volume (Recommended)

```
Container
     │
     ▼
Docker Volume
```

Managed by Docker.

Best for:

- Databases
- Persistent application data
- Production workloads

---

## 2. Bind Mount

Maps a host directory directly.

Example:

```bash
docker run -v $(pwd):/app image-name
```

```
Mac Folder
      │
      ▼
Container /app
```

Best for:

- Development
- Live code editing

---

## 3. tmpfs Mount

Stores data in memory.

```
RAM
 │
 ▼
Container
```

Data disappears when the container stops.

Best for:

- Temporary files
- Sensitive data

---

# Docker Volumes in Local Development

Many developers run PostgreSQL locally:

```
Go API Container

        │
        ▼

PostgreSQL Container

        │
        ▼

Docker Volume
```

This ensures the database survives container recreation.

---

# Docker Volumes in Production

Modern cloud applications usually look different.

```
Users
   │
   ▼

Load Balancer
   │
   ▼

Kubernetes Pods

   │
   ▼

AWS RDS PostgreSQL
```

In this architecture:

- Go application containers are stateless.
- PostgreSQL runs as AWS RDS.
- RDS manages storage automatically.
- Docker volumes are typically **not** used for the application database.

---

# Docker Volumes vs Kubernetes Persistent Volumes

Docker:

```
Container
     │
     ▼
Docker Volume
```

Kubernetes:

```
Pod
 │
 ▼
Persistent Volume Claim (PVC)
 │
 ▼
Persistent Volume (PV)
 │
 ▼
AWS EBS / Azure Disk / GCP Persistent Disk
```

The idea is the same:

Application → Persistent Storage

Only the implementation differs.

---

# Volumes in a CI/CD Pipeline

Suppose your Go application is updated.

```
Git Push
    │
    ▼
CI/CD Pipeline
    │
    ▼
Build Docker Image
    │
    ▼
Push to ECR
    │
    ▼
Deploy to Kubernetes
```

During deployment:

```
Old Container
        │
        ▼
Deleted

New Container
        │
        ▼
Started
```

If your application uses a volume:

```
Old Container

        │
        ▼

Shared Volume

        ▲
        │

New Container
```

The application changes.

The data does not.

---

# Common Docker Volume Commands

Create a volume:

```bash
docker volume create my-volume
```

List volumes:

```bash
docker volume ls
```

Inspect a volume:

```bash
docker volume inspect my-volume
```

Remove a volume:

```bash
docker volume rm my-volume
```

Remove unused volumes:

```bash
docker volume prune
```

---

# Best Practices

- Keep application containers stateless whenever possible.
- Store database data in volumes or managed database services.
- Use bind mounts for local development.
- Never store important data only inside a container.
- Remove unused volumes periodically to free disk space.

---

# Key Takeaways

- Containers are temporary.
- Volumes provide persistent storage.
- A volume survives container recreation.
- Multiple containers can mount the same volume when appropriate.
- Docker volumes are common for local databases.
- Production cloud applications often use managed storage (for example, AWS RDS) instead of Docker volumes.
- Kubernetes uses Persistent Volumes (PV) and Persistent Volume Claims (PVC), which serve the same purpose as Docker volumes at the orchestration level.

---

# Next Topic

- Docker Compose
- Running Multi-Container Applications
- Environment Variables
- Networks + Volumes + Services Together