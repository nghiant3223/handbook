# GoMODULE with Private Git Repository

## Developer Environment

### 1. Set GOPRIVATE environment variable

You can do either `export $GOPRIVATE=<module path>` or `go env -w GOPRIVATE=<module path>`

### 2. Set repository credentials

#### 2.1. Set git configuration

`git config --global url."https://${username}:${access_token}@github.com".insteadOf "https://github.com"`

#### 2.2. Update `~/.netrc` file

```sh
machine <hostname>
login <username>
password <token>
```

## CI/CD or Container Environment

```Dockerfile
# Start from the latest golang base image
FROM golang:alpine

WORKDIR /app
COPY . .

RUN go env -w GOPRIVATE=github.com/ereshzealous

RUN apk add git
RUN git config --global url."https://golang:<access-token>@github.com".insteadOf "https://github.com"

RUN go build -o main .
EXPOSE 8080
CMD ["./main"]
```
