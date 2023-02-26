FROM golang:1.14 AS builder

RUN mkdir /app
WORKDIR /app
COPY . .

# To understand `CGO_ENABLED=0`, see https://www.reddit.com/r/docker/comments/bld85r/help_exec_user_process_caused_no_such_file_or/emnhuok
RUN CGO_ENABLED=0 GOOS=linux go build -o main cmd/main.go

FROM scratch AS production

COPY --from=builder /app/main .
EXPOSE 8080
CMD ["./main"]
