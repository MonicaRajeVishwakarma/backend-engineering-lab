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

Displays all images stored locally. Tell the size of the images also.

---

```bash
docker image ls
```

List all the images

---

## Copy Files Between Build Stages

```bash
COPY --from=builder /app/server .
```

Copies a file from one build stage into another, allowing the final image to contain only the files needed at runtime.

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
## Run Container in Detached Mode

```bash
docker run -d -p 8080:8080 <image-name>
```

---

## View Docker Image Layers

```bash
docker history <image-name>
```

### Example 
```bash
docker history hello
```

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

# Git Commands Cheat Sheet

## Check Git Repository Status

```bash
git status
```

### Purpose

Shows the current state of your repository.

It tells you:

- Modified files
- Untracked files
- Staged files
- Commits waiting to be pushed

Example:

```
modified: docker/01-hello-docker/COMMANDS.md
```

---

## Check Current Branch

```bash
git branch
```

Shows the branch you are currently working on.

Example:

```
* main
```

The `*` indicates the current branch.

---

## View Remote Repository

```bash
git remote -v
```

Shows which GitHub repository your local repository is connected to.

Example:

```
origin  git@github.com:user/backend-engineering-lab.git
```

---

## Add Files to Staging Area

```bash
git add .
```

### Purpose

Moves all changed files into the staging area before committing.

Flow:

```
Working Directory
        |
        |
     git add
        |
        ↓
Staging Area
```

---

## Add a Specific File

```bash
git add <filename>
```

Example:

```bash
git add docker/01-hello-docker/COMMANDS.md
```

Useful when you do not want to commit every change.

---

## Commit Changes

```bash
git commit -m "your message"
```

Example:

```bash
git commit -m "Add docker commands documentation"
```

### Purpose

Creates a snapshot of your staged changes in your local repository.

Important:

A commit does NOT update GitHub.

---

## Push Changes to GitHub

```bash
git push
```

### Purpose

Uploads local commits to the remote GitHub repository.

Flow:

```
Local Repository
        |
        |
    git push
        |
        ↓
GitHub Repository
```

---

## Pull Latest Changes from GitHub

```bash
git pull
```

### Purpose

Downloads and merges changes from GitHub into your local repository.

Used when:

- Someone else changed the repository
- You worked from another machine

---

## View Commit History

```bash
git log
```

Shows previous commits.

Short version:

```bash
git log --oneline
```

Example:

```
abc1234 Add docker commands
def5678 Initial project setup
```

---

## See Differences Before Commit

```bash
git diff
```

Shows changes that are not staged yet.

---

## See Staged Changes

```bash
git diff --staged
```

Shows changes that are ready to be committed.

---

## Create a New Branch

```bash
git checkout -b <branch-name>
```

Example:

```bash
git checkout -b docker-layers
```

Creates and switches to a new branch.

---

## Switch Branch

```bash
git checkout <branch-name>
```

Example:

```bash
git checkout main
```

---

## Delete a Branch

```bash
git branch -d <branch-name>
```

Example:

```bash
git branch -d docker-layers
```

---

# Common Git Workflow

Daily workflow:

```bash
# Check changes
git status

# Stage changes
git add .

# Commit locally
git commit -m "Describe change"

# Push to GitHub
git push
```

---

# Understanding Git Locations

```
Working Directory
        |
        | git add
        ↓
Staging Area
        |
        | git commit
        ↓
Local Repository
        |
        | git push
        ↓
GitHub Repository
```

---

# Commit Message Examples

Good:

```
docker: add hello world container example

docker: document image layers

git: add command reference

go: add http server example
```

Avoid:

```
changes
update
fix stuff
```