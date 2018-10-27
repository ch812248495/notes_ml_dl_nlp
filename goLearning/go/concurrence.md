# **Concurrence**

goroutine is the core of Go's concurrence

```go
package main

import (
    "fmt"
    "runtime"
)

func say(s string){
    for i := 0;i<5;i++{
        runtime.Gosched()
        fmt.Println(s)
    }
}

func main(){
    go say("world")
    say("hello")
}
```

Here is the output

```
// hello
// world
// hello
// world
// hello
// world
// hello
// world
// hello
```

* `runtime.Gosched()` means letting CPU give out the controling right, resume next time.

**channels**

channel is a great mechanism for sharing the memories.

establish a channel:

```go
ci := make(chan int)
cs := make(chan string)
cf := make(chan interface{})
```

channel receive and send data by `<-`

```go
ch <- v    //send v to channel ch
v := <-ch    //receive data from ch and assign to v
```

Here is an example:

```go
package main

import "fmt"

func sum(a []int,c chan int){
    total := 0
    for _, v := range a{
        total += v
    }
    c <- total 
}

func main(){
    a := []int{3,25,6,4,8,1}
    
    c := make(chan int)
    go sum(a[:len(a)/2],c)
    go sum(a[len(a)/2:],c)
    x,y := <-c,<-c
    
    fmt.Println(x,y,x+y)
}
```

**Blockage:**

When reading from the channel\(`value <- chan`\), it will get blocked till its data got received.

When sending data \(`ch <- 5`\) to the channel, it will get blocked till data get read.

No explicit locks are needed.

Non-buffer character makes the concurrence more effective.

**Buffered channels**

The golang also permmits your assigning buffer's space:

```go
ch := make(chan type, value)
```

```go
package main

import "fmt"

func main(){
    c := make(chan int,2)
    c <- 1
    c <- 2
    fmt.Println(<-c)
    fmt.Println(<-c)
}
```

The output should be :

```
1
2
```

First in, first out.



**Range and close**

```go
package main

import(
    "fmt"
)

func fibonacci(n int, c chan int){
    x,y := 1,1
    for i := 0;i<n;i++{
        c <- x
        x,y = y,x+y
    }
    close(c)    //close the channel
}

func main(){
    c := make(chan int,10)
    go fubonacci(cap(c),c)    //cap(c) means the capacity of this channel
    for i := range c{
        fmt.Println(i)
    }
}
```

**Select**

We can use select to monitor the data flow on one interested channel.

```go
package main

import "fmt"

func fibonacci(c,quit chan int){
    x, y := 1,1
    for {
        select {
            case c <- x:
                x, y = y, x+y
            case c <- quit:
                fmt.Println("quit")
                return
        }
    }
}

func main(){
    c := make(chan int)
    quit := make(chan int)
    go func() {
        for i := 0;i<10;i++{
            fmt.Println(<-c)
        }
        quit <- 0
    }()
    fibnacci(c,quit)
}
```



