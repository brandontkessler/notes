# maps

maps keys to values
zero value is nil
`make` returns a map of given type, initialized and ready for use

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

func main() {
	m := make(map[string]Vertex)
	
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])

    m["2"] = Vertex{
        10.0, 80.0,
    }
    fmt.Println(m["2"])
}
```

---

map literals are like struct literals but require keys

```go
var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

// OR

var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967}, // omit Vertex as it's the type name
	"Google":    {37.42202, -122.08408},
}

func main() {
	m["Test"] = Vertex{
		0.0, 80.0,
	}

	fmt.Println(m)
}
```

---

## Mutating maps

* delete an element with `delete(m, key)`
* test that a keys is present w/ two-value assignment `elem, ok = m[key]`
  * If key is in `m`, `ok` is `true`. If not, `ok` is `false`.

```go
func main() {
	m := make(map[string]int)

	m["Answer"] = 42
	fmt.Println("The value:", m["Answer"])

	m["Answer"] = 48
	fmt.Println("The value:", m["Answer"])

	delete(m, "Answer")
	fmt.Println("The value:", m["Answer"])

	v, ok := m["Answer"]
	fmt.Println("The value:", v, "Present?", ok)
}
```