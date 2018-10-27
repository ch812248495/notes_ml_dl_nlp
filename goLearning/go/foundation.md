# Go foundation

**variable definition**

basic method:

```go
var variableName type
var person string
```

multiple variable definition:

```go
var vname1, vname2, vname3 type
```

define the variable and initialize it:

```go
var variableName type = value
```

initialize multiple variables:

```go
var vname1, vname2, vname3 type = v1, v2, v3
```

or simplify it:

```go
var vname1, vname2, vname3 = v1, v2, v3
```

or do the most simplified one:

```go
vname1, vname2, vname3 := v1, v2, v3
```

> Noted that this version should only be adopted inside the functions, or the complation will fail.
>
> So the definition withe the prefix `var` can be used for the global variables.
>
> \_ is a special variable name, all values allocated to it will be deprecated
>
> ```go
> _, b := 34, 35
> ```

Declared but not used variables will trigger mistakes, which is not permmited in go.

** Const **

Const  are those whose values have been determined when compiling.

```go
const constantName = value
//if necessary, allocate the type of the const
const Pi float32 = 3.1415926
const prefix = "astaxie_"
```

**Other types**

Boolean, int, uint, int8, int16, int32, int64, byte, uint8, uint16, uint32, uint64, float32, float64

* rune is equivalent to int32, byte is equivalent to uint8.
* int's lenth is 32, but it is not equal to int32.
* No float, the deflaut one is float64
* complex is supported, complex128\(64+64i\) and complex64\(32+32i\)

```go
var c complex64 = 5+5i
fmt.Printf("Value is: %v",c)
```

**String**

Use `""`or \` \` to define the String in between.

```go
var frenchHello string
var emptyString string = ""
func test(){
    no, yes, maybe := "no", "yes", "maybe"
    japaneseHello := "Konichiwa"
    frenchHello := "Bonjour"
}
```

> Noted that string is not changable
>
> ```go
> var s string = "hello"
> s[0] = 'c'//raise mistake "cannot assign to s[0]"
> ```
>
> here is the method to do the modification
>
> ```go
> s := "hello"
> c := []byte(s) //convert s to []byte
> c[0] = 'c'
> s2 := string(c) //convert it back
> ```
>
> Or use slice to modify the string
>
> ```go
> s := "hello"
> h := "c" + s[1:]
> fmt.Printf("%s\n",h)
> ```

`+` can connect two strings together

```go
s := "hello"
h := "world"
a := s+h
fmt.Printf("%s\n",a)
```

\` \` can preserve the initial format of the string, all things will remain the same.

    m := `Hello
            World`
    fmt.Printf("%s\n",m)

Here is the output:

```
Hello
    World
```

**Error**

The type of error is specially for handling mistakes. There us a package of `errors.`

```go
err := errors.New("emit macho dwarf: elf header corrupted")
if err != nil{
    fmt.Printf(err)
}
```

**Storage of data**

![](/assets/import.png)

**Some skills**

* declaration by groups

> ```go
> import(
>     "fmt"
>     "os"
> )
> const(
>     i = 100
>     pi = 3.1415
>     prefix = "Go_"
> )
> var (
>     i int
>     pi float32
>     prefix string
> )
> ```

* iota enumeration

One more lines of const, one more the value of iota

```go
package main
import (
    "fmt"
)
const (
    x = iota //x = 0
    y = iota //y = 1
    z //z = 2, with omission declaration
)
const v = iota //one more keyword const, reset the value of iota
const (
    h,j,i = iota,iota,iota //all is 0, three should match three
)
const (
    a = iota    //a = 0
    b = "B"
    c,d,e = iota  //c,d,e = 1
)
```

** Default rules**

* variables with capital letter are public, other packages could read the value, variables with lowercase letters are private.
* Same as the fuction names.

** array**

Declaration

```go
var arr [n]type
```

n is the length of the array, type is the elements this array stores,

```go
var arr [10]int
arr[0] = 42
arr[1] = 13
fmt.Println(arr[9]) //The initial value of a[9] is 0
```

Declaration inside the functions:

```go
a := [3]int{1,2,3}
b := [10]int{1,2,3}
c := [...]int{4,5,6} //omit the length, count the number of elements instead
```

Matraix:

```go
doubleArray := [2][4]int{
    {1,2,3,4}
    {5,6,7,8}
}
```

> Noted that when we pass one array into the function, we pass the copy of this array rather than its pointer.
>
> if you wanted to modify the elements anyway, use slice.

**Slice**

Or could be described as "dynamical array", do not need the specification of length.

```go
var fslice []int
```

Initialization:

```go
slice := []byte {'a','b','c'}
```

`slice` could be picked from an existed array by array\[i:j\], array\[i\] is included, array\[j\] is excluded, the length is `j-i`

```go
var ar = [10]int {1,2,3,4,5,6,7,8,9,10}

var a, b []int

a = ar[2,5]

b = ar[3,5]
```

![](/assets/a.png)

When changing the values in the slice, the corresponding value in the array will be changed,too.

The interfaces in `slice`:

![](/assets/b.png)

* `len` can get the length
* `cap` can get the capacity
* `append` can add one or more elements and return the `slice`

capacity could be specified now:

```go
slice = array[2:4:7] //capacity is 7-2
```

**map**

The dictionary in Python, whose index could be many types besides int.

Noted that the declaration should be outside the function, while the memory allocation should be inside the function.

```go
var numbers map[string]int //declaration, should initialize it by make afterwards

numbers = make(map[string]int)
numbers["one"] = 1
numbers["two"] = 2
```

* map is unordered, which should be acquired by `key` rather than `index`
* map has two return, the first one is the value, the second one is the boolean

```go
rating := map[string]float32{"C":5,"Go":4.5,"Python":4.5}
csharpRating, ok := rating["C#"]
if ok{
    fmt.Println("C# is in the map and its rating is ", cssharpRating)
}

delete(rating, "C#")
```

**Differences between make and new**

* make: allocate memory for `map`,`slice`,`channel`,return T type rather than \*T.

* new: allocate memory for various types, the default value is 0, return \*T\(its address\) pointingto T's value

** Zero values**

```go
int     0
int8    0
int32   0
int64   0
uint    0x0
rune    0 //rune的实际类型是 int32
byte    0x0 // byte的实际类型是 uint8
float32 0 //长度为 4 byte
float64 0 //长度为 8 byte
bool    false
string  ""
```

The default padding values when you new the type

