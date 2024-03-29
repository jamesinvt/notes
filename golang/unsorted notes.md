```golang

isString := arg != nil && reflect.TypeOf(arg).Kind() == reflect.String
```

difference between `Sprint` and `Sprintln`

```golang
func (p *pp) doPrint(a []any) {
    prevString := false
    for argNum, arg := range a {
        isString := arg != nil && reflect.TypeOf(arg).Kind() == reflect.String
        // Add a space between two non-string arguments.
        if argNum > 0 && !isString && !prevString {
            p.buf.writeByte(' ')
        }
        p.printArg(arg, 'v')
        prevString = isString
    }
}

// doPrintln is like doPrint but always adds a space between arguments
// and a newline after the last argument.
func (p *pp) doPrintln(a []any) {
    for argNum, arg := range a {
        if argNum > 0 {
            p.buf.writeByte(' ')
        }
        p.printArg(arg, 'v')
    }
    p.buf.writeByte('\n')
}

```
