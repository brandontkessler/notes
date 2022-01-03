# struct

a collection of fields

```go
type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
    fmt.Println(Vertex{Y: 10})
    fmt.Println(Vertex{X: 3, Y: 10})
}
```

accessible using a dot

```go
func main() {
	v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
}
```

---

## struct literals

```go
type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // has type Vertex
	v2 = Vertex{X: 1}  // Y:0 is implicit
	v3 = Vertex{}      // X:0 and Y:0
	p  = &Vertex{1, 2} // has type *Vertex
)
```