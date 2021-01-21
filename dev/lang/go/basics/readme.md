# Basics

## Command

- For packages
  - `go build` builds your package then discards the results.
  - `go install` builds then installs the package in your `$GOPATH/pkg` directory (the result is `.a` files).
- For commands
  - `go build` builds the command and leaves the result the current working directory.
  - `go install` builds the command in a temporary directory then moves it to $GOPATH/bin.
- `go run` build the temporary binary and execute it.
