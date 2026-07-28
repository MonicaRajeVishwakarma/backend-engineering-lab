# History
Earlier a backend application was depended heavily on the machine it ran on.

# VM vs Container

## Virtual Machine

A VM virtualizes hardware.
Each VM has its own operating system.

Pros:
- Strong isolation
- Can run different OS

Cons:
- Heavy
- Slow startup


## Container

A container virtualizes the application environment.
Containers share the host OS kernel.

Pros:
- Lightweight
- Fast startup
- Easy deployment

Cons:
- Less isolation than VMs

# Docker's purpose:
Package the application and its runtime environment into a portable unit.

# Docker has 3 main things
1. Dockerfile : "Take Go, copy my code, build my application, start it."
2. Image : A packaged application.
3. Container : A running instance.


# Hello Docker Go Application

## Build

docker build -t hello .

## Run

docker run -p 8080:8080 hello

## Test

curl localhost:8080

