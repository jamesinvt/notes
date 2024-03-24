---
tags:
  - book
  - notes
  - programming
  - go
  - golang
  - coding
  - test
  - test/foo
  - test/foo/bar
  - test9999
---

    URL to book: https://go.dev/doc/effective_go#introduction

    [!bing!]
    author notes that guide is useful but dated

## basics
    - recommends tabs not spaces
    - c-style comments `/* */` `//`
    - shorter names with a doc comments > long function names

## getters
    - getters and setters not idiomatic most of the time.  it's okay to use them but usually better to omit get and set.  set is sometimes the right choice to include.

```golang

        // example
        owner := obj.Owner()
        if owner != user {
            obj.SetOwner(user)
        }
```

##  naming
    - one method interfaces should be named after the method + -er or some other agent noun
    - if type implements a method with same meaning as a method on a well known type, give it the same name and signature.  instead of `toString()` call method `String()`
    - `MixedCaps` or `mixedCaps` > underscores for multiword names

## semicolons
    - don't need semicolons for the most part.  lexer adds them to any place where a newline comes after a token that could end a statement. 
    - semicolons still used with for loop clauses.  needed if writing code on one line
    - need to put open brace on the same line as control structure(see above semicolon stuff)

## special control statement behaviors
    - `if`, `switch`, `for`, `break`, and `constinue` accept an optional initialization statement
```golang
        // example
        if err := file.Chmod(0664); err != nil {
            log.Print(err)
            return err
        }
```

    omit else when if statement doesn't flow into next statement.  ex: ends with return, break, continue...
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
```golang

        for key, value := range dictionary

        for key := range dictionary

        for _, value := range dictionary

        for pos, char := range "string gang"

        for index, a := range arr
```

    if no expression used in `switch` statement then it switches on true.  using this you can do if else if else chain.
```
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
```

        func stateFromZip(zip string) string {
            switch{
            case '05478': return 'VT'
            case '10003', '10004', '100035': return 'NY'
            }
      }
```
    (book didn't mention `fallthrough` statement)

    use `break` and a `label` if you are in a switch statement within a loop to break out of that loop
```

        Loop:
            for i := 0; i < 10; i++ {
                switch {
                    case i == 5: break Loop
                }
        }
```
    `continue` also allows labels but only for loops

## Switch
    you can use switch statements to discover dynamic types of interface variables. this works through syntax assertion where when the variable and its type is declared in the switch expression every corresponding use of that variable uses that type now
```

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

you can return multiple values
```

func addOne(x int, y int) (int,int) {
    return x + 1, y + 1
}

a = 0
b = 0

c, d = addOne(a, b)
```

can have named return params.  makes it so you don't have to include them in the return.
```
// example
var position = [2]int{42, 3}
func newVertex(offset int) (x int, y int) {
    x = position[0] + offset
    y = position[1] + offset
    return
}
fmt.Println(newVertex(12))

```
named parameters can increase readability(however it might still be best to include the variables in the return)

use `defer` to run a function immediately before a function is returned.  good for stuff like clean up 
```
f, err := os.Open(filename)
defer f.close()
if (idksomething) {
	return "nope" // f.close runs right before returning
}
return string(result) //f.close runs right before returning
```
arguments are evaluated when the defer executes, not when the call executes.  **executed LIFO**
```
func main() {
	for i := 0; i < 5; i++ {
		defer fmt.Printf("%d ", i) 
	}
}
// prints: 4 3 2 1 0
```
can do something like
```
func trace(s string) {
	fmt.Println("Tracing:", s)
	return s
}
func cleanup(s string) {
	fmt.Println("No longer tracing: " s)
}
defer cleanup(trace("a"))
// trace will be called and then when return comes defer will be called with
// the return value that `trace()`returned earlier. defer args evaluated when defer executes, not when 
```
go has two allocation primitives `new` and `make` 


