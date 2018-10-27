# **Object-oriented**

Method belog to struct

```go
package main

import (
    "fmt"
    "math"
)

type Rectangle struct{
    width, height float64
}

type Circle struct {
    radius float64
}

func (r Rectangle) area(){
    return r.width*r.height
}

func (c Circle) area(){
    return c.radius * c.radius * math.Pi
}

func main(){
    r1 := Rectangle{12,2}
    c1 := Circle{10}
    fmt.Println("Area of r1 is: ",r1.area())
}
```

Noted:

* the name of methods can be the same, but if the struct receivers are different, then the methods are different
* calling method by `.`, just like `struct` calling its elements.

![](/assets/1.png)

Not only struct, all other build-in types, self-defined types could be the object to apply methods.

Here are some examples of self-defined types:

```go
type ages int //alias
type money float32

type months map[string]int
```

Here is another comprehensive example:

```go
package main

import "fmt"

const(
    white = iota
    black
    blue
    red
    yellow
)

type Color byte    //uint8

type Box struct{
    width,height, depth float64
    color Color
}

type BoxList []Box  //a slice whose elements are boxes

func (b Box) Volume() float64{
    return b.width * b.height * b.depth
}

func (b *Box) SetColor(c color){
    b.color = c
}

func (bl BoxList) BiggestColor() Color{
    v := 0.00
    k := Color(white)
    for _,b := range bl{    //_,b corresponding to index and value, b is the strcuts
        if bv := b.Volume();bv>v{
            v = bv
            k = b.color
        }
    }
    return k
}

func (bl BoxList) PaintItBlack(){
    for i := range bl{    //here i is index, we skip the value, we can also write like this:
    //fot _,b := range bl
        bl[i].SetColor(black)
    //b.SeetColor(black)
    }
}

func (c Color) String() string{
    strings := []string {"white","black","blue","red","yellow"}
    return string[c]
}
```

**Use pointers as receiver**

```go
func (b *Box) SetColor(c color){
    b.color = c
}
```

Here we may use `*b.color` instead, cause the input is a pointer\(the address\), however golang knows what we want to do, so we do not need to take too much time specifying those troublesome issues.

Likely, we may also use `&bl[i]` since the receiver of SetColor is a pointer\(address\), but similarily, do not focus on those details. 

```go
func (bl BoxList) PaintItBlack(){
    for i := range bl{    //here i is index, we skip the value, we can also write like this:
    //fot _,b := range bl
        bl[i].SetColor(black)
    //b.SeetColor(black)
    }
}
```

**Inherit**

If the embeded segment realize some methods, then the struct who includes it can inherit those methods, too.

```go
package main

import "fmt"

type Human struct{
    name string
    age int
    phone string
}

type Student struct{
    Human
    school string
}

func (h *Human) Sayhi(){
    fmt.Println("Hi, I am", h.name, "you can call me on ",h.phone)
}

func main(){
    make := Student{Human{"Mark",25,"222-222"},"MIT"}
    make.SayHi()
}
```

**Overwrite**

```go
package main

import "fmt"

type Human struct{
    name string
    age int
    phone string
}

type Student struct{
    Human
    school string
}

func (h *Human) Sayhi(){
    fmt.Println("Hi, I am", h.name, "you can call me on ",h.phone)
}

func (s *Student) Sayhi(){
    fmt.Println("Hi, I am a student")
}
func main(){
    make := Student{Human{"Mark",25,"222-222"},"MIT"}
    make.SayHi()
}
```











