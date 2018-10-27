# Hello, world

```go
package main
import "fmt"

func main() {
    fmt.Printf("Hello, world or 你好，世界 or καλημ ́ρα κóσμ or こんにちはせかい\n")
}
```

This is the simplest program to print "Hello, world".

Go program is organized to gether by packages, the first line indicate the package which it belongs to. The package "main" indicates that it is a independent package\(a executable after compiling\). Other .go will generate .a file in $GOPATH/pkg/darwin\_amd64.

> Every independently executable .go should possess a package `main` including a entry function called main without parameters or return values.

The concept packages here is like the packages in Python, which is great for modularization and reusablity.

Go is born to support UTF-8, one of Go's founder is also the advocator of UTF-8

