FROM golang:1.25.1-alpine AS builder

RUN adduser -D -g '' appuser

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o main cmd/api/main.go

FROM alpine:3.19

RUN apk --no-cache add ca-certificates tzdata

WORKDIR /root/

COPY --from=builder /etc/passwd /etc/passwd
COPY --from=builder /app/main .

USER appuser

EXPOSE 8081

CMD ["./main"]
