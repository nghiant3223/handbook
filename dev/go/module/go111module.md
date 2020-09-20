# GO111MODULE

Reference: https://dev.to/maelvls/why-is-go111module-everywhere-and-everything-about-go-modules-24k

## GOPATH and GOMODULE

There are two modes in go: GOPATH and GOMODULE.

In GOPATH mode, `go get` would fetch all the sources using their import path and store them in `$GOPATH/src`. There was no versioning and the 'master' branch would represent a stable version of the package, which means `go get` always fetches latest version of the dependency.

GOMODULE is introduces in Go 1.11. In this mode, tagged version of dependency is stored in `go.mod` keeping track of each packages's version. `go get` will dowload specific version of dependency and put them in `$GOPATH/pkg/mod` directory.

## The `GO111MODULE` environment variable

GO111MODULE is an environment variable that can be set when using go for changing how Go imports packages. One of the first pain-points is that depending on the Go version, its semantics changes.

### `GO111MODULE` with Go 1.11 and 1.12

- `GO111MODULE=on` forces Go to be in GOMODULE mode even if the project is in your `GOPATH` (requires go.mod to work).

- `GO111MODULE=off` forces Go to be in GOPATH mode, even outside of `$GOPATH`.

- `GO111MODULE=auto` is the default value of `GO111MODULE` when it is not set, the mode which Go run in is specified in below table:

|      Location     | `go.mod` avaiable |       Mode       |
|:-----------------:|-------------------|:----------------:|
| Inside `$GOPATH`  |        Yes        |      GOPATH      |
| Inside `$GOPATH`  |         No        |      GOPATH      |
| Outside `$GOPATH` |        Yes        |     GOMODULE     |
| Outside `$GOPATH` |         No        | GOMODULE (error) |

Whenever you are in your GOPATH and you want to do an operation that requires Go modules (e.g., go get a specific version of a binary), you need to do:
```
GO111MODULE=on go get github.com/golang/mock/tree/master/mockgen@v1.3.1
```

