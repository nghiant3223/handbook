# GO111MODULE

## GOPATH and GOMODULE

There are two modes in Go: **GOPATH** and **GOMODULE**.

In GOPATH mode, `go get` would fetch all the sources using their import path and store them in `$GOPATH/src`. There was no versioning and the 'master' branch would represent a stable version of the package, which means `go get` always fetches latest version of the dependency.

GOMODULE is introduces in Go 1.11. In this mode, tagged version of dependency is stored in `go.mod` keeping track of each packages's version. `go get` will dowload specific version of dependency and put them in `$GOPATH/pkg/mod` directory.

## The `GO111MODULE` environment variable

GO111MODULE is an environment variable that can be set when using go for changing how Go imports packages. One of the first pain-points is that depending on the Go version, its semantics changes.

### `GO111MODULE` with Go 1.11 and 1.12

- `GO111MODULE=on` forces Go to be in GOMODULE mode even if the project is in your `$GOPATH` (requires go.mod to work).
- `GO111MODULE=off` forces Go to be in GOPATH mode, even outside of `$GOPATH`.
- `GO111MODULE=auto` is the default value of `GO111MODULE` when it is not set, the mode in which Go run is specified in below table:

|      Location     | Is `go.mod` avaiable |       Mode       |
|:-----------------:|:-----------------:|:----------------:|
| Inside `$GOPATH`  |        Yes        |      GOPATH      |
| Inside `$GOPATH`  |         No        |      GOPATH      |
| Outside `$GOPATH` |        Yes        |     GOMODULE     |
| Outside `$GOPATH` |         No        | GOMODULE (error) |

Whenever you are in your `$GOPATH` and you want to do an operation that requires Go modules (e.g., go get a specific version of a binary), you need to do:
```
GO111MODULE=on go get github.com/golang/mock/tree/master/mockgen@v1.3.1
```

### `GO111MODULE` with Go 1.13

Using Go 1.13, `GO111MODULE`'s default (auto) changes:


|      Location     | Is `go.mod` available |       Mode       |
|:-----------------:|:------------------:|:----------------:|
| Inside `$GOPATH`  |        Yes         |     GOMODULE     |
| Inside `$GOPATH`  |         No         |      GOPATH      |
| Outside `$GOPATH` |        Yes         |     GOMODULE     |
| Outside `$GOPATH` |         No         |     GOMODULE     |

### `GO111MODULE` with Go 1.14

The `GO111MODULE` variable has the same behavior as with Go 1.13.

Note that some slight changes in behaviors unrelated to `GO111MODULE` happened:

- The `vendor/` is picked up automatically. That has the tendency of breaking Gomock (issue) which were unknowingly not using `vendor/` before 1.14.
- You still need to use `cd && GO111MODULE=on go get` when you don't want to mess up your current project’s `go.mod` (that's so annoying).

### `GO111MODULE` with Go 1.15

- `GO111MODULE=on` forces Go to be in GOMODULE mode even if the project is in your `$GOPATH` (**not requires go.mod to work**).
- `GO111MODULE=off` forces Go to be in GOPATH mode, even outside of `$GOPATH`.
- `GO111MODULE=auto` is the default value of `GO111MODULE` when it is not set, the mode in which Go run is specified in below table:

|      Location     | Is `go.mod` available |       Mode       |
|:-----------------:|:------------------:|:----------------:|
| Inside `$GOPATH`  |        Yes         |     GOMODULE     |
| Inside `$GOPATH`  |         No         |      GOPATH      |
| Outside `$GOPATH` |        Yes         |     GOMODULE     |
| Outside `$GOPATH` |         No         |      GOPATH      |

### So, why is `GO111MODULE` everywhere?!

Now that we know that `GO111MODULE` can be very useful for enabling the Go Modules behavior, here is the answer: that's because `GO111MODULE=on` allows you to select a version. Without Go Modules, `go get` fetches the latest commit from master. With Go Modules, you can select a specific version based on git tags.

I use `GO111MODULE=on` very often when I want to switch between the latest version and the HEAD version of gopls:
```
GO111MODULE=on go get golang.org/x/tools/gopls@latest
GO111MODULE=on go get golang.org/x/tools/gopls@master
GO111MODULE=on go get golang.org/x/tools/gopls@v0.1
GO111MODULE=on go get golang.org/x/tools/gopls@v0.1.8
GO111MODULE="on" go get sigs.k8s.io/kind@v0.7.0
```

Again, these dependencies are stored in `$GOPATH/pkg/mod`.

## Reference

- [Why is GO111MODULE everywhere and everything about modules](https://dev.to/maelvls/why-is-go111module-everywhere-and-everything-about-go-modules-24k)

