# pointers

A pointer holds the memory address of a value

```go
func main() {
	i, j := 42, 2701

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer
	*p = 21         // set i through the pointer
	fmt.Println(i)  // see the new value of i

	p = &j         // point to j
	*p = *p / 37   // divide j through the pointer
	fmt.Println(j) // see the new value of j
}
```


In most cases, don't use pointers: [see why](https://medium.com/@meeusdylan/when-to-use-pointers-in-go-44c15fe04eac)

## Cases for pointers

1. Copying large structs - how large? unclear, use benchmarking to consider
2. Mutability - there are alternatives to mutability, see examples below
3. API consistency - use pointers everywhere if you need at least one 

## Mutability Examples

In the case below, using values will not change the name of 'p'
The return output will still be Richard.

```go
type person struct {
 name string
}

func main() {
 p := person{"Richard"}
 rename(p)
 fmt.Println(p)
}

func rename(p person) {
 p.name = "Dave"
}
```

This can be rewritten using pointers as:

```go
func main() {
 p := person{"Richard"}
 rename(&p)
 fmt.Println(p)
}

func rename(p *person) {
 p.name = "Dave"
}
```

As an alternative to requiring pointers, the functions can simply be 
rewritten using below:

```go
func main() {
 p := person{"Richard"}
 p = rename(p)
 fmt.Println(p)
}

func rename(p person) person {
 p.name = "Dave"
 return p
}
```

## Case for consistency

```go
func (p *person) rename(s string) {
   p.name = s 
}
func (p *person) printName() {
  fmt.Println(p.name)
}
```

over

```go
func (p *person) rename(s string) {
   p.name = s 
}
func (p person) printName() {
  fmt.Println(p.name)
}
```