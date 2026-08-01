# Docker Multi-Stage Build (Go)

This example demonstrates how to use **Docker multi-stage builds** to create a smaller and cleaner production image for a Go application.

---

## Why Multi-Stage Builds?

A normal Docker build:

```dockerfile
FROM golang:1.24

WORKDIR /app

COPY . .

RUN go build -o server

CMD ["./server"]
```

creates an image containing:

- Go compiler
- Go build tools
- Source code
- Application binary

The final image becomes large because the application only needs the compiled binary at runtime.

Multi-stage builds solve this by separating:

1. **Build Environment** - used to compile the application
2. **Runtime Environment** - used only to run the application

---

# Dockerfile Flow

The Dockerfile contains two stages.

---

# Stage 1: Builder

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /app

COPY . .

RUN go build -o server
```

The builder stage contains:

- Go compiler
- Go tooling
- Source code
- Dependencies

After building:

```
Builder Stage

/app

├── main.go
├── go.mod
├── Dockerfile
└── server
```

The output is:

```
server
```

which is the compiled Go binary.

---

# Stage 2: Runtime

```dockerfile
FROM alpine

WORKDIR /app

COPY --from=builder /app/server .

CMD ["./server"]
```

A new clean image is created using Alpine Linux.

Only the compiled binary is copied from the builder stage.

Final image:

```
Runtime Stage

/app

└── server
```

The final image does not contain:

- Go compiler
- Go tools
- Source code
- Build dependencies

---

# Complete Dockerfile

```dockerfile
FROM golang:1.24 AS builder

WORKDIR /app

COPY . .

RUN go build -o server


FROM alpine

WORKDIR /app

COPY --from=builder /app/server .

CMD ["./server"]
```

---

# Build the Image

Run this command from the project directory:

```bash
docker build -t hello-multi .
```

Explanation:

- `docker build` → creates a Docker image
- `-t hello-multi` → gives the image a name/tag
- `.` → uses the current directory as the build context

---

# Run the Container

Run in foreground:

```bash
docker run -p 8080:8080 hello-multi
```

Run in detached mode:

```bash
docker run -d -p 8080:8080 hello-multi
```

The `-d` option runs the container in the background.

---

# Check Running Containers

```bash
docker ps
```

Example:

```
CONTAINER ID   IMAGE          PORTS
abc123         hello-multi    8080->8080
```

---

# Test the Application

Send an HTTP request:

```bash
curl localhost:8080
```

Request flow:

```
Client

   |
   ↓

Docker Container

   |
   ↓

Go HTTP Server

   |
   ↓

Response
```

---

# Compare Image Sizes

Check images:

```bash
docker images
```

Normal Go image:

```
golang:1.24
+
application

= Large Image
```

Multi-stage image:

```
alpine
+
Go binary

= Small Image
```

Benefits:

- Smaller Docker images
- Faster deployments
- Faster downloads
- Lower storage usage
- Reduced security surface

---

# Important Concepts Learned

## Build Image vs Runtime Image

## Build Image

Example:

```
golang:1.24
```

Contains:

```
✅ Go compiler
✅ Go tools
✅ Source code
✅ Build dependencies
```

Purpose:

```
Compile the application
```

---

## Runtime Image

Example:

```
alpine
```

Contains:

```
✅ Application binary
✅ Minimal Linux environment
```

Does not contain:

```
❌ Go compiler
❌ Source code
❌ Build tools
```

Purpose:

```
Run the application
```

---

# Understanding Stage Names

The name after `AS` is user-defined.

Example:

```dockerfile
FROM golang:1.24 AS builder
```

`builder` is just a stage name.

It can be changed:

```dockerfile
FROM golang:1.24 AS compile-stage
```

Then the copy command must use the same name:

```dockerfile
COPY --from=compile-stage /app/server .
```

---

# Docker Layer Flow

The build creates layers:

```
Layer 1
FROM golang:1.24

Layer 2
WORKDIR /app

Layer 3
COPY source code

Layer 4
RUN go build

Layer 5
FROM alpine

Layer 6
COPY binary

Layer 7
CMD
```

Only the final stage becomes the final Docker image.

---

# Cleanup Commands

Remove stopped containers:

```bash
docker container prune
```

Remove unused images:

```bash
docker image prune
```

---

# Key Takeaways

- Docker images are built using layers.
- Multi-stage builds allow multiple temporary build environments.
- Only the final stage becomes the production image.
- Builder images contain tools required for compilation.
- Runtime images contain only what is required to execute the application.
- `COPY --from=<stage-name>` copies files between stages.

---

# Next Docker Topics

1. Docker Networking
2. Docker Volumes
3. Docker Compose
4. Running Databases with Containers
5. Preparing Containers for Kubernetes