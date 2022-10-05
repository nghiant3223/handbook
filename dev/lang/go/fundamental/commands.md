# Commands

## `go build`

- main package: build the package and leaves the binary at the current working directory
- non-main packge: build package then discards the results

## `go install`

- main package: build the package in a temporary directory then moves it to `$GOPATH/bin`
- non-main package: 
  - When module-aware mode is disabled, packages are installed in the directory `$GOPATH/pkg/$GOOS_$GOARCH`
  - When module-aware mode is enabled, packages are built and cached but not installed

## `go run`

- `go run` build the temporary binary and execute it.
