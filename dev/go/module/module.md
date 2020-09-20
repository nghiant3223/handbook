# GOMODULE

## Build commands

All commands that load information about packages are module-aware. This includes:

- go build
- go fix
- go generate
- go get
- go install
- go list
- go run
- go test
- go vet

When run in module-aware mode, these commands use go.mod files to interpret import paths listed on the command line or written in Go source files. These commands accept the following flags, common to all module commands.

- The -mod flag controls whether go.mod may be automatically updated and whether the vendor directory is used.
  - -mod=mod tells the go command to ignore the vendor directory and to automatically update go.mod, for example, when an imported package is not provided by any known module.
  - -mod=readonly tells the go command to ignore the vendor directory and to report an error if go.mod needs to be updated.
  - -mod=vendor tells the go command to use the vendor directory. In this mode, the go command will not use the network or the module cache.
  - By default, if the go version in go.mod is 1.14 or higher and a vendor directory is present, the go command will act as if -mod=vendor were used. Otherwise, the go command will act as if -mod=mod were used.
- The -modcacherw flag instructs the go command to create new directories in the module cache with read-write permissions instead of making them read-only. When this flag is used consistently (typically by setting GOFLAGS=-modcacherw in the environment or by running go env -w GOFLAGS=-modcacherw), the module cache may be deleted with commands like rm -r without changing permissions first. The go clean -modcache command may be used to delete the module cache, whether or not -modcacherw was used.
- The -modfile=file.mod flag instructs the go command to read (and possibly write) an alternate file instead of go.mod in the module root directory. The file's name must end with .mod. A file named go.mod must still be present in order to determine the module root directory, but it is not accessed. When -modfile is specified, an alternate go.sum file is also used: its path is derived from the -modfile flag by trimming the .mod extension and appending .sum.