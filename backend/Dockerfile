FROM golang:1.22.7-alpine AS builder

RUN apk add --no-cache git ca-certificates tzdata wget

RUN adduser -D -g '' -s /sbin/nologin appuser

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download && go mod verify

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build \
    -ldflags='-w -s -extldflags "-static"' \
    -a -installsuffix cgo \
    -o main ./cmd/api

FROM alpine:latest

RUN apk --no-cache add ca-certificates wget tzdata

RUN adduser -D -g '' -s /sbin/nologin appuser

WORKDIR /root/

COPY --from=builder /app/main .

USER appuser

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD ["wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:8081/health"] || exit 1

EXPOSE 8081

CMD ["./main"]
