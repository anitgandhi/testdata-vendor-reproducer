## testdata-vendor-reproducer

This is a minimal reproducer of a potential bug in `go mod vendor` wherein a `testdata` dir is incorrectly included in the vendor directory, specifically when there is `embed` directives referencing the `testdata` in a `*_test.go` file.

What I ran (using Go 1.18)
```bash
go mod tidy
go mod vendor
```

Observe that in [the vendored files](vendor/github.com/golang-migrate/migrate/v4/source/iofs/), nowhere is there an embed directive. Specifically, [this upstream test file](https://github.com/golang-migrate/migrate/blob/v4.15.1/source/iofs/example_test.go#L15-L16) is correctly not vendored, and as such, the `testdata/migrations` dir should not have been vendored.

### Notes/Observations

- It reproduces when the `go` directive in [go.mod](go.mod) is 1.17
- It reproduces when the `go version` is 1.17

### `go list -json` output

```json
→ go list -json
{
        "Dir": "/Users/agandhi/git/testdata-vendor-reproducer",
        "ImportPath": "github.com/anitgandhi/testdata-vendor-reproducer",
        "Name": "main",
        "Target": "/Users/agandhi/go/bin/testdata-vendor-reproducer",
        "Root": "/Users/agandhi/git/testdata-vendor-reproducer",
        "Module": {
                "Path": "github.com/anitgandhi/testdata-vendor-reproducer",
                "Main": true,
                "Dir": "/Users/agandhi/git/testdata-vendor-reproducer",
                "GoMod": "/Users/agandhi/git/testdata-vendor-reproducer/go.mod",
                "GoVersion": "1.18"
        },
        "Match": [
                "."
        ],
        "Stale": true,
        "StaleReason": "stale dependency: github.com/golang-migrate/migrate/v4/source",
        "GoFiles": [
                "main.go"
        ],
        "Imports": [
                "github.com/golang-migrate/migrate/v4/source/iofs"
        ],
        "Deps": [
                "bytes",
                "errors",
                "fmt",
                "github.com/golang-migrate/migrate/v4/source",
                "github.com/golang-migrate/migrate/v4/source/iofs",
                "internal/abi",
                "internal/bytealg",
                "internal/cpu",
                "internal/fmtsort",
                "internal/goarch",
                "internal/goexperiment",
                "internal/goos",
                "internal/itoa",
                "internal/oserror",
                "internal/poll",
                "internal/race",
                "internal/reflectlite",
                "internal/syscall/execenv",
                "internal/syscall/unix",
                "internal/testlog",
                "internal/unsafeheader",
                "io",
                "io/fs",
                "math",
                "math/bits",
                "net/url",
                "os",
                "path",
                "reflect",
                "regexp",
                "regexp/syntax",
                "runtime",
                "runtime/internal/atomic",
                "runtime/internal/math",
                "runtime/internal/sys",
                "sort",
                "strconv",
                "strings",
                "sync",
                "sync/atomic",
                "syscall",
                "time",
                "unicode",
                "unicode/utf8",
                "unsafe"
        ]
}
```