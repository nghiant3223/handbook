# Dependency Version

The go command resolves imports by using the specific dependency module versions listed in go.mod. When it encounters an import of a package not provided by any module in go.mod, the go command automatically looks up the module containing that package and adds it to go.mod, using:

- The latest version (aka. latest tagged stable (non-[prerelease](https://semver.org/#spec-item-9)) version)
- Or else, the latest tagged [prerelease](https://semver.org/#spec-item-9) version
- Or else, the latest untagged commit

The `golang.org/x/text` version `v0.0.0-20170915032832-14c0d48ead0c` is an example of a [pseudo-version](https://golang.org/cmd/go/#hdr-Pseudo_versions), which is the go command's version syntax for a specific untagged commit.

In addition to go.mod, the go command maintains a file named go.sum containing the expected [cryptographic hashes](https://golang.org/cmd/go/#hdr-Module_downloading_and_verification) of the content of specific module versions.

The go command uses the go.sum file to ensure that future downloads of these modules retrieve the same bits as the first download, to ensure the modules your project depends on do not change unexpectedly, whether for malicious, accidental, or other reasons. Both go.mod and go.sum should be checked into version control.
