# methods

* methods can be assigned on types
* a method is a function with a *receiver* argument
* the receiver appears in its own argument list between the `func` keyword and the method name.


```go
package main

import (
	"fmt"
)

type Vertex struct {
	X, Y float64
}

func (v Vertex) Sum() float64 {
	return v.X + v.Y
}

func main() {
	v := Vertex{3, 4}
	fmt.Println(v.Sum())    // returns 7 
	fmt.Println(v)          // returns {3 4} -- does not return the method
}
```

---

methods can also be assigned on other types, not just structs

