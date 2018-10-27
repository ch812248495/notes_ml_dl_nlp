# Interface

Interface is a group of method signatures, continuin with the example before, if `Student` realize three methods:

`SayHi`,`Sing`,`BorrowMoney`,while `Employee` realize `SayHi`,`Sing`,`SpendSalary`, the combination of those methods is defined as `interface`.

Here `SayHi` and `Sing` are realized by both structs, so those two structs belong to this interface\(the combination of `SayHi` and `Sing`\).

Simply speaking, the `interface` defines a group of methods, if one object realize all methods of this interface, then this object realize this interface.

```go
type Human struct {
    name string
    age int
    phone string
}

type Student struct {
    Human //匿名字段Human
    school string
    loan float32
}

type Employee struct {
    Human //匿名字段Human
    company string
    money float32
}

//Human对象实现Sayhi方法
func (h *Human) SayHi() {
    fmt.Printf("Hi, I am %s you can call me on %s\n", h.name, h.phone)
}

// Human对象实现Sing方法
func (h *Human) Sing(lyrics string) {
    fmt.Println("La la, la la la, la la la la la...", lyrics)
}

//Human对象实现Guzzle方法
func (h *Human) Guzzle(beerStein string) {
    fmt.Println("Guzzle Guzzle Guzzle...", beerStein)
}

// Employee重载Human的Sayhi方法
func (e *Employee) SayHi() {
    fmt.Printf("Hi, I am %s, I work at %s. Call me on %s\n", e.name,
        e.company, e.phone) //此句可以分成多行
}

//Student实现BorrowMoney方法
func (s *Student) BorrowMoney(amount float32) {
    s.loan += amount // (again and again and...)
}

//Employee实现SpendSalary方法
func (e *Employee) SpendSalary(amount float32) {
    e.money -= amount // More vodka please!!! Get me through the day!
}

// 定义interface
type Men interface {
    SayHi()
    Sing(lyrics string)
    Guzzle(beerStein string)
}

type YoungChap interface {
    SayHi()
    Sing(song string)
    BorrowMoney(amount float32)
}

type ElderlyGent interface {
    SayHi()
    Sing(song string)
    SpendSalary(amount float32)
}
```

Men interface could be realized by Human, Student and Employee, one interface could be realizedd by a number of structs. Similarily, one struct could also realize more than one interface, Student realizes Men and YoungChap.

All types realize empty interface\(without method\):

```go
interface{}        //empty interface
```

**fmt.Println\(\)**

An constantly used output function, all types could be used as input which will be output as its initial form.

Here is how it is realized in the souce code:

```go
type Stringer interface(){
    String() string
}
```

String is the pre-defined interface object, String\(\) is a method whose return type is string, which means all types which could realize String\(\) method can be disposed as the input and thus call fmt.Pringln\(\).

So, if our struct could realize the so-called String\(\) method, then our struct could also be compatible with fmt.Println\(\).

```go
package main
import (
    "fmt"
    "strconv"
)

type Human struct {
    name string
    age int
    phone string
}

// 通过这个方法 Human 实现了 fmt.Stringer
func (h Human) String() string {
    return "❰"+h.name+" - "+strconv.Itoa(h.age)+" years -  ✆ " +h.phone+"❱"
}

func main() {
    Bob := Human{"Bob", 39, "000-7777-XXX"}
    fmt.Println("This Human is : ", Bob)
}
```

Here our manully defined struct Human overloaded the method String\(\), so when we call the fmt.Println\(\), the customized output could be surpported.

**The value of interface**

So, what should we put into one interface? if we define a variable of interface, then this variable could store any object which has realized this interface.For the example above, if we define a variable `m`,then m can sore `Human`,`Student`,`Employee`.

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
    age int
    phone string
}

type Employee struct{
    Human    //anaonymous segment
    company
    loan float32
}

func (h Human)SayHi(){
    fmt.Println("I am a human")
}

func (s Student)SayHi(){
    fmt.Println("I am a studnet")
}

func (e Employee)SayHi(){
    fmt.Println("I am a employee")
}

type Men interface{
    SayHi()
}

func main(){
    mike := Student{Human{"Mike",25,"222-222"},"MIT",0}
    same := Employee{Human{"Sam",36,"333-333"},"Golang Inc.",1000}
    var i Men    //defien a interface
    i = mike    //i can store student
    i.SayHi()
    
    i = tom
    i.SayHi()
}
```

**Empty interface**

All types realized the empty interface.

```go
var a interface{}
var i int = 5
s := "Hello world"
//a can store any types
a = i
a = s
```

**Comma-ok mechanisim\(a little confusing\)**

We can overload  the specific function for one type so as to make it suitable for some interfaces, but how can we know which kinds of type can realize a specific interface?

To judge a parameter's type, here is a syntax for this:

```go
value, ok = element.(T)
```

element is the interface , T is the type which we want to know whether it corresponds to element,

**Interface insertion **

Interface1 could be a insertion of interface2 so that we do not have to write the same interfaces again and again.

```go
type Interface interface {
    sort.Interface //嵌入字段sort.Interface
    Push(x interface{}) //a Push method to push elements into the heap
    Pop() interface{} //a Pop elements that pops elements from the heap
}
```

Interface could include all the interface of sort:

```go
type Interface interface {
    // Len is the number of elements in the collection.
    Len() int
    // Less returns whether the element with index i should sort
    // before the element with index j.
    Less(i, j int) bool
    // Swap swaps the elements with indexes i and j.
    Swap(i, j int)
}
```

An other example of interface is:

```go
// io.ReadWriter
type ReadWriter interface {
    Reader
    Writer
}
```

ReadWrite interface is simply the superposition of interface Reader and Write.

**Reflection**

1.get the reflect type of i first

```go
t := reflect.TypeOf(i)    //得到类型的元数据,通过t我们能获取类型定义里面的所有元素
v := reflect.ValueOf(i)   //得到实际的值，通过v我们获取存储在里面的值，还可以去改变值
```

1. get the corresponding value

```go
tag := t.Elem().Field(0).Tag  //获取定义在struct里面的标签
name := v.Elem().Field(0).String()  //获取存储在第一个字段里面的值
```

1. get the type and value  of i

```go
var x float64 = 3.4
v := reflect.ValueOf(x)
fmt.Println("type:", v.Type())
fmt.Println("kind is float64:", v.Kind() == reflect.Float64)
fmt.Println("value:", v.Float())
```

Here is the result:

```
type: float64
kind is float64: true
value: 3.4
```

If you execute the following code:

```go
v.SetFloat(7.1)
```

the program will report a mestery error:

```
panic: reflect: reflect.Value.SetInt using unaddressable value
```

Here when we set the value of v, the passed input x is the copy of the original one, changing it is meaningless and illegal, this is the meaning of "unaddressable".

Similarly, passing the point of x is OK if you intend to change the value of x

```go
var x float64 = 3.4
p := reflect.ValueOf(&x)
v := p.Elem()
v.SetFloat(7.1)
```

Changing the address of x so as to change the value of it is workable.

