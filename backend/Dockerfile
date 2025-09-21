FROM golang:1.23.12-alpine AS builder

RUN apk add --no-cache git ca-certificates tzdata

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download && go mod verify

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build \
    -ldflags='-w -s -extldflags "-static"' \
    -a -installsuffix cgo \
    -o main ./cmd/api && \
    chmod +x main

FROM alpine:latest

RUN apk --no-cache add ca-certificates tzdata

RUN adduser -D -g '' -s /sbin/nologin appuser

RUN mkdir -p /app && chown appuser:appuser /app

WORKDIR /app

COPY --from=builder --chown=appuser:appuser --chmod=755 /app/main ./main

USER appuser

EXPOSE 8081

CMD ["./main"]
