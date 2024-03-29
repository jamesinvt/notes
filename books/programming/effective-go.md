---
tags:
  - book
  - notes
  - programming
  - go
  - golang
  - coding
---

URL to book: https://go.dev/doc/effective_go#introduction

disclaimer: these are just scratchpad notes as im reading this.
            if you aren't the auther of this repo you should not 
            be using any inf as reference as 90% of it is wrong

> [!Bing] HEY! LISTEN!
> Author notes this is still a good guide but dated

## basics

- recommends tabs not spaces
- c-style comments `/* */` `//`
- shorter names with a doc comments > long function names

## getters

getters and setters not idiomatic most of the time
it's okay to use them but usually better to omit get and set
set is sometimes the right choice to include

```golang

// example
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

##  naming

one method interfaces name should be an agent noun
usally the method name + -er ex: `Wrapper`

if type implements a method with same meaning as one on a well known type
give it the same name and signature 
instead of `toString()` call method `String()`

`MixedCaps` or `mixedCaps` > underscores for multiword names

## semicolons

don't need semicolons for the most part lexer adds them to any place
where a newline comes after a token that could end a statement

semicolons still are still used sometimes.
  ex: for loop clauses needed if writing code on one line

need to put open brace on the same line as control structure
  (see above semicolon stuff)

## special control statement behaviors

`if`, `switch`, `for`, `break`, and `constinue`
accept an optional initialization statement
```golang
// example
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```

omit else when if statement doesn't flow into next statement
    ex: ends with `return`, `break`, `continue`
```golang

f,err := os.Open(name)
if err != nil {
    log.Print(err)
    return err
}
codeUsing(f)
```

no do-while loops.  go unifies for and while
```golang

//c for2
for i:= 0; i < 10; i++ { }
// c while
i := 0; 
for i < 10 { i++ }
for { }
```

use `range` when looping array, slice, string, map,  or reading from a channel
```golang

for key, value := range dictionary
for key := range dictionary
for _, value := range dictionary
for pos, char := range "string gang"
for index, a := range arr
```

if no expression used in `switch` statement then it switches on true
using this you can do if else if else chain
```golang
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```

no default fall through but there is comma separated cases
```golang

func stateFromZip(zip string) string {
    switch{
    case '05478': return 'VT'
    case '10003', '10004', '100035': return 'NY'
    }
}
```
(book didn't mention `fallthrough` statement)

break out of loops from a swith using a `label` after a `break` statement
```golang

Loop:
    for i := 0; i < 10; i++ {
        switch {
            case i == 5: break Loop
        }
}
```
`continue` also allows labels but only for loops

## switch

You can use switch statements to discover dynamic types of interface variables
this works through syntax assertion
when the variable and its type is declared in the switch expression
every corresponding use of that variable uses that type now
```golang

func fn() string {
  return "yeet"
}

var t interface{}
t = fn()
switch t := t.(type) {
  case string: fmt.Printf("type is string %s\n", t)
  case int: fmt.Printf("type is integer %d\n", t)
}
```

## return values
you can return multiple values
```golang

func addOne(x int, y int) (int,int) {
    return x + 1, y + 1
}

a = 0
b = 0

c, d = addOne(a, b)
```
## named return params

can have named return params. sames vars will be used in the return
```golang
// example
var position = [2]int{42, 3}
func newVertex(offset int) (x int, y int) {
    x = position[0] + offset
    y = position[1] + offset
    return
}
fmt.Println(newVertex(12))

```
named parameters can increase readability
(however it might still be best to include the variables in the return)

## defer

use `defer` to run a function immediately before a function is returned.
good for stuff like clean up 
```golang
f, err := os.Open(filename)
defer f.close()
if (idksomething) {
    return "nope" // f.close runs right before returning
}
return string(result) //f.close runs right before returning
```

arguments are evaluated when the defer executes, not when the call executes
    note: **executed LIFO**
```golang
func main() {
    for i := 0; i < 5; i++ {
        defer fmt.Printf("%d ", i) 
    }
}
// prints: 4 3 2 1 0
```
can do something like
```golang
func trace(s string) {
    fmt.Println("Tracing:", s)
    return s
}
func cleanup(s string) {
    fmt.Println("No longer tracing: " s)
}
defer cleanup(trace("a"))
// trace will be called and then when return comes
// defer will be called with
// the return value that `trace()`returned earlier.
// defer args evaluated when defer executes, not when 
```

## allocation
go has two allocation primitives

### new

does not initialize memory. instead it allocates ZEROED storage
and returns a pointer to a value of type T

good idea to make your data structures use the zero values
to avoid initialization.  objects should be able to get to work 
as soon as possible.  using zero value allows instant use
    ex: `Buffer` zero state means empty buffer ready to use
```golang

func NewPatient(name string, age int) *Patient {
    // p := new(Patient)
    // p.name = name
    // p.age = age
    // return p
    // so many lines!! simplify with:
    p := Patient{name, age}
    return &f
}
```
note the fn returing the address of the local variable
in go the variable will survive after the function returns

composite types fields must be ready, however using named expressions
composite literals can also be used with arrays, slices, amd maps
```golang

a := []string { "foo", "bar" }
m := map[int]string { 0: "red" }
```
> [!BING] README

clarifying something from this section this I didn't get at first:
composite slice literals DO initialize memory and also return a value 
of `type T`  and not of  a pointer to `type T` like `new()` does

they also don't really drive home the point that these are refrences to
underlying types. below is the structure of the action []int data structure
it holds the cap, len, and a pointer to the underlying array.  first ex it
is nil and the others are initialized.
```golang
a := new([]int) // *[]int [cap: 0, len: 0, nil ]
b := []int{} // []int [cap: 0, len: 0, []]
c := &[]int{} // *[]int [cap: 0, len: 0, []]
d := make([]int,0) // []int [cap: 0, len: 0, []]
```
notice how make initialized with 0 len produces the same thing as the
composite literal. also important is `&[]int{}` and `new()` difference!

and when talking about composite literals
> composite literal, which is an expression that  creates a new instance 
> each time it is evaluated
not sure the implication of this
can old data get lost?
is there duplucation problems?


### make

`make(T, args)` creates slices, maps, and channels only
it returns initialized(not zeroed) value of type T(not *T)
```golang

// bad
var a *[]int = new([]int)
*a = make([]int, 5, 10)
//good
b := make([]int, 10)
```
make does not return a pointer so use new or take the address explicitly
```golang

a := new([]int)
b := &[]int{}
c := make([]int,0)
p := &c // *[]int [cap: 0, len: 0, []
```

## arrays

- in go arrays are values
    (in languages like c annd js they are passed by reference)
- assigning one array to another copies all the elements
```golang

a := [3]int{1,2,3}
var b [3]int

b = a // b = a = [ 1, 2, 3, 4 ]
b[0] = 4 // b = [ 4, 2, 3], a = [ 1, 2, 3 ]
```
in languages like js, since arrays are passed by reference when assigned
so doing `b[0]` would have resulted in both `a` and `b` being `[ 4, 2, 3 ]`

- the size of an array is part of its type. 
    ex: `[6]type` and `[9]type` are different

- ... will infer the array size
```golang

[...]int{1,2,3} // [3]int
```
- passing pointers to arrays can mimic c-like behavior.  passing by value
can be expensive

- use slices instead

### slices

most go programming will use slices over arrays.  arrays are fine for things
like transformation matrices since they have specific, unchanging dimensions

slices hold references to an underlying array.  when a variable is assigned
a slice, both variables refer to the same slice. assign a slice to another 
and they both point to the same array
if a function takes a slice as an argument the caller sees the changes!)
```golang

arr = [3]int{1,2,3}
s := arr[0:1]
s2 := s
s2[0] = 9 // s = [9,2,3], b = [9,2,3]
```
    (note: similar to pointers)

can easily take "slices" out of arrays
```golang

arr := [5]int{1,2,3,4,5}
pizza : = arr[1:3] // 2 ,3 
pizza[0] = 9 // pizza [9, 3], arr [1,9,3,4,5]
```

the length of a slice may be changed as long as it still fits with in the
array it's sliced from
```golang

// example Append fn from book that allocates more mem if slice cap is reached
func Append(slice, data []byte) []byte }
    sliceLength := len(slice) //len is part of the `builtin` pkg
    dataLength := len(data)
    if( sliceLength + dataLength > cap(slice) {
        newSlice := make([]byte, (sliceLength + dataLength) * 2 )
        copy(newSlice, slice) // `builtin` pkg
        slice = newSlice
    }
    slice = slice[0:sliceLength+dataLength]
    copy(slice[sliceLength:], data)
    return slice
}
```

### 2D slices
```golang
type GuessWhoBoard [5][5] string //2d array
type LinesOfText [][]byte //slice of a slice
```
- slices make it possible to have inner slices all be variable length

- if slices grow or shrink might have to allocate each slice,
  or if not it might be better to allocate a single array 
  and point slices to it
```golang
// example of allocating each sub slice individually
chessboard := make([][]rune, YSize) // allocate first line
for i := range chessboard {
    chessboard[i] = make([]rune, XSize) // allocate new lines
}

// example of one main board allocation

chessboard := make([]rune, XSize*YSize) // allocate full board(not a 2d array)
screen := make([][]rune, YSize) // allocate first 

for i := range chessboard {
    screen[i], chessboard := chessboard[:XSize], chessboard[XSize:]
}
```



### maps

- defintion from the book: built-in data structure that associate values
of one type (the key) to another type (the element/value)

- the key can be of any type for which the equality operator is defined.
    ex: ints, floats, complex numbers, strings, pointers, structs, arrays,
        and interfaces(as long as the dynamic type supports equality)
        (we love woke interfaces here)

- slices cannot be used as map keys because equality is not defined on them

- maps are also references to an underline data structer.  just like slices.
  also like slices, if you pass to a function.  any changes in that fn
  will be reflected in the caller as well 

constructing a map:
```golang

var[key T]val T{

}
// ex:
var numbers = map[string]int {
    "one": 1,
    "two": 2,
}
a := numbers["one"] // 1
```
if map key doesn't exist it will return the zero value for the entries type
one way to distinguish between an actual value of 0 and zeroed 0 is by
using multiple returns.  the second return being some form of "ok" or "err"
    (note: they call this "comma ok" idiom)
```golang
    if num, ok = number(name string) // get int num from word
    if num, ok = number("one") // num = 1, ok = true
    if num, ok = number("three") // num = 0, ok = false
    _, present = number("four") // preset = false
    _, preset = number("two") // present = true
```

use `delete(numbers, "one"` to delete a map entry

### printing

- functions in `fmt` are all capitalized(how go exports functions)
- functions like `Printf` and `Sprintf` take in a format string where
  funcs like `Println` do not require that

- `Fprint(os.Stdout, "i'm only ", 34, "\n")` takes in a `io.Writer`
  as the first arguement

- go doesn't take flags for things like signness or size, instead uses type

- use `%v`(value) as a catch all to produce same format Println would give
  also can print any value(arrays,slices, structs, maps)
  use `%+v` to print the fields of structs as well

- for maps print fns sort output lexcographically by key

-`%q` and `%#q` will include the quotes and things like \n in output 

- `%x` generates a hex string add a space and it adds a space between bytes

- `%T` will print the type of a value

Create a custom `String() string` on a type to print custom formatting every
time a print function is called
```golang
func (p *Person) String() string {
    return fmt.Sprintf("Name: %s Age: %d", p.name, p.age)
}
```
- if you need to print values of type T as well as the pointer
  the receiver for String must be of value type

- Put a `v...interface{}` as the final param of `Printf` to mark an 
  arbitrary numbers of params  `v` which is of type `[]interface{}` 
  but if passed to another variadic function(takes a variable amount of args)

- use `...int` to mark a variadic number of int types

### append
built in append fn
```golang
func append(slice []T, elements ...T)[]T // in builtin pkg
```
book mentions:
> You can't write a function where the type T is determined by the caller
> That's why append is built in: it needs support from the compiler

append will append the elements to the end of the slice and return the result.
underlying array may change elsewhere in the program,  so result is returned

use `...` operator
```golang

x := []int{1,2,3}
y := append(x, 4, 5, 6} // works but we can do better
z := []int{4,5,6}
x = append(x, y...) // append([1,2,3], 4, 5, 6)
```

just passing in the array would not work since it wouldn't be the right type
for the builtin append fn
```golang
// would be append([1,2,3], [4,5,6]) or append([]int, []int) 
// see above for fn signature for append
x = append(x, y)
```

## initialization

### constants

- created at compile time, even when defined as local.

- can only be string, numbers, bools, and characters.

- must be a constant expression(because it has to be evaulated at compile time)

create enumberated constanst with the `iota` keyword
```golang
const (
    _ = iota // can use _ to "waste" the first value if you dont want the zero
    ONE = iota  // now starts off at 1
    TWO
    THREE
)
fmt.Println("%s, %s, $s", ONE, TWO, THREE) // 1, 2, 3
```

### init function

- can have a(or multiple) `init` function with no params to setup program state

- `init` is called after variable declarations and package initialization

## methods

### pointers vs values

- functions can be defined for any named tyoe (except pointer or an interface

- receiver doesn't have to be a struct

define a method to on a type with receiver params
```golang
type GuestList []string
func (list GuestList) AddGuest(str string) []string {
    return append(list, str)
}
func main() {
    list := GuestList{"Bob"}
    list = list.AddGuest("Sarah") // ["Bob", "Sarah"]
}
```

can use a pointer as a receiver to avoid having to return
```golang
type GuestList []string
func (list *GuestList) AddGuest(str string) {
     *list = append(*list, str)
}
func main() {
    list := GuestList{"Bob"}
    list.AddGuest("Sarah") // ["Bob", "Sarah"]
}
```
- !! value methods can be invoked on pointers and values,
  but pointer methods can only be invoked on pointers !! this rule is because
  pointer methods can modify receiver.  if it were to get a value,
  instead of a reference it will get a copy and changes lost
  get around this rule using the `&` to make value addressable


