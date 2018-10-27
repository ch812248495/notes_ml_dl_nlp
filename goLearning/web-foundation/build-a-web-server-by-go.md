```go
package main

import (
    "fmt"
    "net/http"
    "strings"
    "log"
)

func sayhelloName(w http.ResponseWriter, r *http.Request){
    r.ParseForm()
    fmt.Println(r.Form)
    fmt.Println("path",r.URL.Path)
    fmt.Println("scheme",r.URL.Scheme)
    fmt.Println(r.Form["url_long"])
    for k,v := range r.Form{
        fmt.Println("key:", k)
        fmt.Println("val:",strings.Join(v,""))
    }
    fmt.Fprint(w,"Hello astaxie!")
}

func main(){
    http.HandleFunc("/",sayhelloName)
    err := http.ListenAndServe(":9090",nil)
    if err != nil{
        log.Fatal("listenAndServer: ",err)
    }
}
```

If you enter:

```
http://localhost:9090/?url_long=111&url_long=222
```

```
map[url_long:[111 222]]
path /
scheme 
[111 222]
key: url_long
val: 111222
map[]
path /favicon.ico
scheme 
[]
```



