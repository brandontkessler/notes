# variables

---

var declares a list of variables all of type being the last command
It can be at package or function level

```go
var a, b, c bool

func main() {
	var i int
	fmt.Println(i, c, a, b)
}
```

---

Variables can include initializers (default values)
The type can be omitted in this case

```go
var i, j int = 1, 2
var c, python, java = true, false, "no!"
```

---

Short variable assignment `:=` can be used inside of functions
Outside functions, they cannot

```
func main() {
	var i, j int = 1, 2
	k := 3

	fmt.Println(i, j, k)
}
```

