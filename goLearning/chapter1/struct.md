# Struct

We can define a object and its attributes like this:

```go
type person struct{
    name string
    age int
}

var P person
P.name = "CAO_Heng"
P.age = 25
```

Inside the function, you can also initialize the object like this:

```go
P := person{"Tom",25}
```

**Embeded segment**

```go
package main

import "fmt"

type Human struct{
    name string
    age int
    weight int
}

type Student struct{
    Human //Embeded segment, all elements in Human should be included here
    speciality string
}

```

You can also visit the embeded segments like this:

```go
Mark.Human = Human{"Mark",55,220}
Maek.Human.age -= 1
```

A comprehensive example:

```go
package main

import "fmt"

type Skill []string

type Human struct{
    name string
    age int
    weight int
}

type Student struct{
    Human    //string
    Skills    //self-defined slice
    int    //build-in type as the anonymous segment
    speciality string
}

func main(){
    jane := Student{Human:Human{"Jane",35,100},speciality:"Math"}
    
    jane.Skills = []string{anatomy}
    
    jane.Skills = append(jane.Skills,"physics","golang")
    
    jane.int = 3
}
```



