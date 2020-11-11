# GoMODULE with Private Git Repository

## Developer Environment

### 1. Set GOPRIVATE environment variable

Do one of the following:

#### 1.1. Using `exprort`

`export $GOPRIVATE=<module path>`

#### 1.2. Using `go env`

`go env -w GOPRIVATE=<module path>`

### 2. Set repository credentials

Do one of the following:

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

RUN apk add git
RUN go env -w GOPRIVATE=github.com/ereshzealous
RUN git config --global url."https://${username}:${access_token}@github.com".insteadOf "https://github.com"
RUN go build -o main .

EXPOSE 8080
CMD ["./main"]
```

## Reference

[Go Modules with Private Git Repository
](https://medium.com/swlh/go-modules-with-private-git-repository-3940b6835727)
