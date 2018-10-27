# This is the notes written for the learning of GoLang

**Structure**

The stucture of a typical GoLang program should be like this:

```
$GOPATH
bin/
    mathapp
pkg/
    darwin_amd64/
         mymath.a
         github.com/
              astaxie/
                   beedb.a
src/
    mathapp
          main.go
    mymath/
          sqrt.go
    github.com/
           astaxie/
                beedb/
                    beedb.go
                    util.go
```

* bin/ is responsable for the execuable after compiling
* pkg/ is responsable for the private packages and downloaded packages
* src/ is for the surce code

**Go commands**

* go build: Compile the source code and the related packages if necessary.

> For normal packages, if you intend to generate corresponding files in $GOPATH/pkg, use go install instead.
> For package including "main", one executable file will be generated afterwards in the current path

*  go clean: delete those unnecessary realaying files
*  go fmt: formating the file you select.
*  go get: dramatically and remotely get the packages from the server, 
*  go install: generate .a or executable files and move it to $GOPATH/pkg or $GOPATH/bin





