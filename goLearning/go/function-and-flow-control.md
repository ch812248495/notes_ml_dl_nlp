# Function and flow control

**flow control**

**if:**

```go
if x>10{
    fmt.Println("x is greater than 10")
}else{
    fmt.Println("x is less than 10")
}
```

The condition judegement allow one variable's declaration which only function in this logic judgement part

```go
if x:= computedValue(); x>10{
    fmt.Println("x is greater than 10")
}else if x<3{
    fmt.Println("x is less than 3")
}else{
    fmt.Println("x is greater than 3 and less than 10")
}

fmt.Println(x) //Here will raise some mistakes since x is not available here
```

**For:**

```go
package main
import "fmt"

func main(){
    sum := 0
    for index := 0;index < 10;index++{
        sum += index
    }
    fmt.Println("sum is equal to", sum)
}
```

expression1 and expression2 could be omitted:

```go
sum := 1
for ;sum<1000;{
    sum += sum
}
```

break will shut down the loop, continue will break out this loop

```go
for index := 10;index>0; index--{
    if index == 5{
        break //continue
    }
    fmt.Println(index)
}
//break: 10 9 8 7 6
//continue: 10 9 8 7 6 4 3 2 1
```

for can coordinate with range for `slice` and `map` :

```go
for key,value := range mapName{
    fmt.Println("map's key is:",key)

}
```

You can drop the unuesd values by introducing \_:

```go
for _,v := range mapName{
    fmt.Println("map's value is:", v)
}
```

**Switch**

```go
switch interger{
    case 4:
        fmt.Println("The integer was <= 4")
        fallthrough
    case 5:
        fmt.Println("The integer was <= 5")
        fallthrough
    case 6:
        fmt.Println("The integer was <= 6")
        fallthrough
    default:
        fmt.Println("default case")
}
```

All case defautly possess the `break`, if we use `fallthrough`, then when the first matched case was found, the following cases will still be judged.

**Function**

```go
func funcName(input1 type1, input2 type2, input3 type3)(output1 type1, output2 type2){
    return value1, value2
}
```

* output1 output2 could be omitted, 

```go
package main
import "fmt"

func SumAndProduct(A int, B int)(int, int){
    return A+B, A*B
}

func main(){
    x := 3
    y := 4
    xPLUSy, xTIMESy := SumAndProduct(x,y)
}
```

**Changing parameters**

```go
func myfunc(arg ...int){}
```

`arg` is a slice accepting undetermined parameters whose type is `int`

**Pointer**

When we deliverying parameters from one function to another, we delivery the copy rather than the quotation, so we cannot change the value of the parameters by calling a function, if we intend to do so, pass the pointer.

```go
package main

import "fmt"

//简单的一个函数，实现了参数+1的操作
func add1(a *int) int { // 请注意，
	*a = *a+1 // 修改了a的值
	return *a // 返回新值
}

func main() {
	x := 3

	fmt.Println("x = ", x)  // 应该输出 "x = 3"

	x1 := add1(&x)  // 调用 add1(&x) 传x的地址

	fmt.Println("x+1 = ", x1) // 应该输出 "x+1 = 4"
	fmt.Println("x = ", x)    // 应该输出 "x = 4"
}
```

Here we pass the `*int a`, so the returned `*a` can change the x to x1.

The advantages of passing the pointer:

* multiple functions can manipulate one object.
* passing pointers is efficient and space-saving\(8 bytes\),  when you would pass the struct or a big object, passing the pointer is a great method.
* `channel`,`slice`,`map` 's realizing mechanisms are like pointers

**defer**

defer realize the stack, in functions, defer will be called before return.

```go
func ReadWrite() bool {
	file.Open("file")
	defer file.Close()
	if failureX {
		return false
	}
	if failureY {
		return false
	}
	return true
}
```

Here is another usage, print the output reversely:

```go
for i := 0; i < 5; i++ {
	defer fmt.Printf("%d ", i)
}
```

**Define function as the value or type**

Pass this function as the value:

```go
package main

import "fmt"

type testInt func(int) bool // 声明了一个函数类型

func isOdd(integer int) bool {
	if integer%2 == 0 {
		return false
	}
	return true
}

func isEven(integer int) bool {
	if integer%2 == 0 {
		return true
	}
	return false
}

// 声明的函数类型在这个地方当做了一个参数

func filter(slice []int, f testInt) []int {
	var result []int
	for _, value := range slice {
		if f(value) {
			result = append(result, value)
		}
	}
	return result
}

func main(){
	slice := []int {1, 2, 3, 4, 5, 7}
	fmt.Println("slice = ", slice)
	odd := filter(slice, isOdd)    // 函数当做值来传递了
	fmt.Println("Odd elements of slice are: ", odd)
	even := filter(slice, isEven)  // 函数当做值来传递了
	fmt.Println("Even elements of slice are: ", even)
}	
```

Taking f's place to be testInt\(\)



**Panic and recover:**

Go cannot thrown exceptions as Java or C++, that is why panic and recover exist. But use them cautiously.

```go
var user = os.Getenv("USER")

func init(){
    if user == ""{
        panic("no value for $USER")
    }
}
```

This defer function can judge whether there is a panic function when executing the function

```go
func throwsPanic(f func())(b bool){
    defer func(){
        if x:=recover();x!=nil{
            b = true
        }
    }()
    f()
    return
}
```

* Conming accross the `panic` function, the function will immediately break off and jump to `return`, however the defer function will still be executed, so, if we put the recover function into the `defer`, we can detect whether we come accross the panic and take some measures.

**`main` and `init` **

These two reserved functions are forbidden to possess any input or output.`main` is the entry of the package `main`, `init` is the entry of all packages.

![](/assets/6.png)

**import**

When we inport something, the function load packages from the path abtained from environment variable `GOROOT`.

We can also import our own packages by absolute paths:

```go
import "shorturl/model" //load $GOPATH/shorturl/model
```

Other methods to import packages:

```go
import {
    . "fmt"
}
```

or

```go
import {
    f "fmt"
}
```

then we can call functions of "fmt" by :

```go
import {
    Println("hello world")    //the first one
    f.Println("hello world")    //the second one
}
```



```go
import {
    _ "github.com/ziutek/mysql/godrv"
}
```

No function will be included, but the `init()` will still be executed

