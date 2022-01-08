Pointers are variables that hold the location in memory where a value is stored. 

```go
var x int32 = 10
var y bool = true
pointerX := &x
pointerY := &y

var pointerZ *string
```

The `&` is the *address* operator. 

```go
x := 10
pointerToX := &x
fmt.Println(pointerToX) // prints a memory address
fmt.Println(*pointerToX) // prints 10
z := 5 + *pointerToX
fmt.Println(z) // prints 15
```

The `new` function creates a pointer variable. It returns a pointer to zero-value instance of the provided type.

In almost all cases, avoid using the pointers.

```go
// Don't do this
func MakeFoo(f *Foo) error {
    f.Field1 = "val"
    f.Field2 = 20
    return nil
}

// Do this
func MakeFoo() (Foo, error) {
    f := Foo{
        Field1: "val",
        Field2: 20,
    }
    return f, nil
}
```

The only time you should use pointer parameters to modify a variable is whne the function expects an interface. This is typical when working w/ JSON

```go
f := struct {
    Name string `json:"name"`
    Age int `json:"age"`
}
err := json.Unmarshal([]byte(`{"name" : "Bob", "age": 30}`), &f)
```

# slices as buffers

When reading data form an external resource, this is the norm:

```go
r = open_resource()
while r.has_data() {
    data_chunk = r.next_chunk()
    process(data_chunk)
}
close(r)
```

The problem with this pattern is that every time we iterate through, we allocate another chunk even tho each one is only used once, allocating a lot of unnecessary memory. 

Idiomatic Go means avoiding unneeded allocations. Rather than returning a new allocation each time we read from the data source, create a slice of bytes once and use it as a buffer to read data from the data source:

```go
file, err := os.Open(fileName)
if err != nil {
    return err
}

defer file.Close()

data := make([]byte, 100)
for {
    count, err := file.Read(data)
    
    if err != nil {
        return err
    }

    if count == 0 {
        return nil
    }

    process(data[:count])
}
```

In this code, we create a buffer of up to 100 bytes and each time through the loop, we copy the next block of bytes (up to 100) into the slice. 

# reducing garbage collector workload

When referring to "garbage", programmers are talking about "data that has no more pointers pointing to it." 