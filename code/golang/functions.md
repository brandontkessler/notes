# Functions

---

If two or more consecutive named function params share a type, omit the 
type from all but the last.

```go
func add(x, y int) int {
	return x + y
}
```

---

Naked returns - A return statement without arguments returns the named
return values

```go
func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}
```

Only use this for short functions.

---

## Defer statements

defers the execution of a function until surrounding func returns
defer calls are pushed into a stack and so returned LIFO order

```go
func main() {
	defer fmt.Println("world")

	defer fmt.Println("hello")
    
    fmt.Println("Defer Test:")
}
```

## Function closures

```go
package main

import "fmt"

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int {
	x, y := 0, 1
	
	return func() int {
		x, y = y, x+y
		return y - x
	}
}

func main() {
	f := fibonacci() // function has to be assigned
	for i := 0; i < 10; i++ {
		fmt.Println(f())
        // Each time it loops it will be able to access the x,y from the
        // previous run.

        // fmt.Println(fibonacci()()) - will not work
	}
}
```