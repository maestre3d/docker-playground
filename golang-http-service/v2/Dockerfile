# Builder section
FROM golang:1.19-alpine3.17 as builder

WORKDIR /coinlog

# Git is required for fetching the dependencies.
RUN apk update && apk add --no-cache git

# Setup deps modules
ADD go.mod go.sum /coinlog/
RUN go mod download
RUN go mod verify
RUN go mod tidy -v

ADD . .

RUN CGO_ENABLED=0 GOOS=linux go build -o /coinlog-http ./cmd/http-api/main.go

# Runtime section
FROM alpine:3.17

ARG USER="coinlog"

LABEL maintainer = "Coinlog <docker@coinlog.xyz>"
LABEL org.name="Coinlog"
LABEL org.image.title="Coinlog HTTP REST API"

# Required by health checks
RUN apk --no-cache add curl

RUN adduser \
        -g "log daemon user" \
        --disabled-password \
        ${USER}

COPY --from=builder /coinlog-http /usr/local/bin/coinlog-http

EXPOSE 8080
RUN chown -R ${USER}:${USER} /var
RUN chmod 766 /var
USER ${USER}:${USER}

ENTRYPOINT ["/usr/local/bin/coinlog-http"]
