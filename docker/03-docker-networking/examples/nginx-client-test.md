# Docker Container Communication Test

This example demonstrates how two containers communicate using a Docker network.

Architecture:

```
test-network


+----------------+
| client         |
| alpine         |
+----------------+
        |
        |
        ↓
      HTTP
        |
        |
+----------------+
| web            |
| nginx          |
+----------------+
```

---

# Step 1: Create Network

```bash
docker network create test-network
```

Check:

```bash
docker network ls
```

---

# Step 2: Start Nginx Container

```bash
docker run -d \
--name web \
--network test-network \
nginx
```

Check:

```bash
docker ps
```

The nginx container is running.

---

# Step 3: Start Client Container

```bash
docker run -it \
--name client \
--network test-network \
alpine sh
```

You are now inside the client container.

Example:

```
/ #
```

---

# Step 4: Install Curl

Inside Alpine:

```bash
apk add curl
```

---

# Step 5: Call Nginx Container

Run:

```bash
curl http://web
```

Expected response:

```html
Welcome to nginx!
```

---

# What Happened?

The client container did not know the nginx IP address.

It used:

```
web
```

Docker automatically resolved:

```
web

↓

nginx container IP
```

using Docker DNS.

---

# Important Learning

This works:

```
curl http://web
```

because both containers are connected to:

```
test-network
```

This does not work:

```
curl http://localhost
```

because localhost means:

```
the current container
```

not nginx.

---

# Cleanup

Stop containers:

```bash
docker stop web client
```

Remove containers:

```bash
docker rm web client
```

Remove network:

```bash
docker network rm test-network
```