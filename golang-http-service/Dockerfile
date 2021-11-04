# IMPORTANT NOTE: Do NOT forget to add the .dockerignore file to reduce image size

# Builder section
FROM golang:1.17-alpine3.14 as builder
LABEL maintainer = "Alonso Ruiz <aruiz@neutrinocorp.org>"

# Git is required for fetching the dependencies.
RUN apk update && apk add --no-cache git

# PLACEHOLDER: Add here local deps such as Kernel Context (from DDD terminology), use WORKDIR to use multiple workspaces

# Setup service
WORKDIR /src/service
COPY go.mod .
COPY go.sum .
RUN go mod download
# Copy files and directories from current workspace context to the docker container workdir
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -a -installsuffix cgo -o service-http ./path/to/executable/main.go

# Runtime section
FROM alpine:3.14 as runtime

# CA Certificate module is required to use TLS certificates
RUN apk --no-cache add ca-certificates
ENV neutrino_http_use_tls=true
WORKDIR /root/
# Copy the binary built by builder container
COPY --from=builder /src/service/service-http .
# Copy TLS certificates
COPY --from=builder /src/service/path/to/tls/certs certs
EXPOSE 443
CMD ["./service-http"]
