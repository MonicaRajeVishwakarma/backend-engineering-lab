# Docker Networking Commands Cheat Sheet

---

# List Docker Networks

```bash
docker network ls
```

Shows all Docker networks.

Example:

```
NETWORK ID     NAME       DRIVER

abc123         bridge     bridge
xyz456         host       host
```

---

# Create a Network

```bash
docker network create my-network
```

Creates a custom bridge network.

---

# Inspect a Network

```bash
docker network inspect my-network
```

Shows:

- Connected containers
- Container IP addresses
- Network configuration

---

# Remove a Network

```bash
docker network rm my-network
```

Removes a Docker network.

---

# Run Container With Network

```bash
docker run \
--name api \
--network my-network \
image-name
```

Starts a container attached to a network.

---

# Connect Existing Container

```bash
docker network connect my-network container-name
```

Adds an existing container to a network.

---

# Disconnect Container

```bash
docker network disconnect my-network container-name
```

Removes container from a network.

---

# Inspect Container Network Details

```bash
docker inspect container-name
```

Shows:

- IP address
- Networks
- Ports
- Environment details

---

# Check Running Containers

```bash
docker ps
```

---

# Check All Containers

```bash
docker ps -a
```

Includes stopped containers.

---

# Run Container With Port Mapping

```bash
docker run -p 8080:8080 image-name
```

Format:

```
HOST_PORT:CONTAINER_PORT
```

Example:

```
localhost:8080

      ↓

container:8080
```

---

# Run Container In Background

```bash
docker run -d image-name
```

Detached mode.

---

# Enter Running Container

```bash
docker exec -it container-name sh
```

Example:

```bash
docker exec -it client sh
```

---

# Test Container Communication

Inside a container:

```bash
curl http://container-name
```

Docker resolves:

```
container-name

↓

container IP
```

using internal DNS.