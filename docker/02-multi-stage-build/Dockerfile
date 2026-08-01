FROM golang:1.24 AS builder

WORKDIR /app

COPY . .

RUN go build -o server

FROM alpine

WORKDIR /app

COPY --from=builder /app/server .

CMD ["./server"]