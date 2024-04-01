
https://www.youtube.com/watch?v=tUO0cnSaSy4


Basics

in go you dont explicitly implement the interfaces.  just create a method
with the same signature as the interface

```golang
type Writer interface {
    Write([]byte) (int, error)
}

type ConsoleWriter struct 

func (cw ConsoleWriter) Write(data []byte) (int, error) {
    n, err := fmt.Println(string(data))
    return n, err
}
```

