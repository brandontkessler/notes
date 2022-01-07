# simulating named and optional parameters

Functions cannot take named or optional parameters. To simulate this, define a struct that has the fields that match the desired parameters and pass the struct to the func. 

```go
type MyFuncOpts struct {
    FirstName string
    LastName string
    Age int
}

func MyFunc(opts MyFuncOpts)
error {
    // do something here
}

func main() {
    MyFunc(MyFuncOpts {
        LastName: "Patel",
        Age: 50,
    })

    MyFunc(MyFuncOpts {
        FirstName: "Joe",
        LastName: "Smith",
    })
}
```

# variadic input parameters

Indicate it w/ three dots (...) before the type.

```go
func addTo(base int, vals ...int) []int {
    out := make([]int, 0, len(vals))
    for _, v := range vals {
        out = append(out, base+v)
    }
    return out
}

func main() {
    fmt.Println(addTo(3))
    fmt.Println(addTo(3, 2))
    fmt.Println(addTo(3, 2, 4, 6, 8))
    
    a := []int{4, 3}
    fmt.Println(addTo(3, a...))
    fmt.Println(addTo(3, []int{1, 2, 3, 4, 5}...))
}
```

# functions as variables

Functions that have the exact same number and type of parameters meet the same `type` signature.

```go
// These are all of the same signature
func add(i int, j int) int { return i + j }
func sub(i int, j int) int { return i - j }
func mul(i int, j int) int { return i * j }
func div(i int, j int) int { return i / j }

// Create a map with key of a string that maps to a func that takes
// two integers and returns an integer
var opMap = map[string]func(int, int) int {
    "+": add,
    "-": sub,
    "*": mul,
    "/": div,
}

func main() {
    expressions := [][]string{
        []string{"2", "+", "3"},
        []string{"2", "-", "3"},
        []string{"2", "*", "3"},
        []string{"2", "/", "3"},
        []string{"2", "%", "3"},
        []string{"two", "+", "three"},
        []string{"5"},
    }

    for _, expression := range expressions {
        if len(expression) != 3 {
            fmt.Println("invalid expression", expression)
            continue // move to next in loop
        }

        p1, err := strconv.Atoi(expression[0])
        if err != nil {
            fmt.Println(err)
            continue
        }

        op := expression[1]
        opFunc, ok := opMap[op]
        if !ok {
            fmt.Println("unsupported operator:", op)
            continue
        }

        p2, err := strconv.Atoi(expression[2])
        if err != nil {
            fmt.Println(err)
            continue
        }

        result := opFun(p1, p2)
        fmt.Println(result)
    }
}
```

# func type declarations

An alternative to the above:

```go
type opFuncType func(int, int) int

var opMap = map[string]opFuncType {
    // same as before
}

// Instead of this:
// var opMap = map[string]func(int, int) int {
//     same as before
// }
```

One advantage is documentation - it's useful to give something a name.

# defer

This is used as part of cleanup activities that need to happen after a func. Delays invocation of a function until the surrounding function exits. Multiple defer closures are acceptable and run in LIFO format after the return statement. 

```go
func main() {
    if len(os.Args) < 2 {
        log.Fatal("no file specified")
    }
    
    f, err := os.Open(os.Args[1])
    if err != nil {
        log.Fatal(err)
    }

    defer f.Close()

    data := make([]byte, 2048)
    
    for {
        count, err := f.Read(data)
        os.Stdout.Write(data[:count])

        if err != nil {
            if err != io.EOF {
                log.Fatal(err)
            }
            break
        }
    }
}
```

There is no way to read values returned by a defer function:

```go
defer func() int {
    return 2 // there's no way to read this value
}()
```

### database cleanup using named return values

```go
func DoSomeInserts(ctx context.Context, db *sql.DB, value1, value2 string) (err error) {
    tx, err := db.BeginTx(ctx, nil)
    if err != nil {
        return err
    }

    defer func() {
        if err == nil {
            err = tx.Commit()
        }

        if err != nil {
            tx.Rollback()
        }
    }()

    _, err = tx.ExecContext(ctx, "INSERT INTO FOO (val) values $1", value1)
    if err != nil {
        return err
    }

    // use tx to do more db inserts here
    return nil
}
```

# go is call by value

Go is a *call by value* language. It means when you supply a variable for a parameter to a function, Go always makes a copy of the value of the variable. 

```go
type person struct {
    age  int
    name string
}

func modifyFails(i int, s string, p person) {
    i = i * 2
    s = "Goodbye"
    p.name = "Bob"
}

func main() {
    p := person{}
    i := 2
    s := "Hello"
    modifyFails(i, s, p)
    fmt.Println(i, s, p) // prints 2 Hello {0 }
}
```

Modifying the struct does not actually edit the struct.

This is different if we use a map or slice but that's because they are implemented as pointers. Every type in Go is a value type. Sometimes the value is a pointer.