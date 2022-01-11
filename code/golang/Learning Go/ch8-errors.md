# examples

```go
func calcRemainderAndMod(numerator, denominator int) (int, int, error) {
    if denominator == 0 {
        return 0, 0, errors.New("denominator is 0"
    }

    return numerator / denominator, numerator % denominator, nil
}

func main() {
    numerator := 20
    denominator := 3
    remainder, mod, err := calcRemainderAndMod(numerator, denominator)

    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
    fmt.Println(remainder, mod)
}
```

# use strings for simple errors

```go
// The first way - errors.New
func doubleEven(i int) (int, error) {
    if i % 2 != 0 {
        return 0, errors.New("only even numbers are processed")
    }
    return i * 2, nil
}

// The second way - fmt.Errorf
// This allows use of formatting verbs
func doubleEven(i int) (int, error) {
    if i % 2 != 0 {
        return 0, fmt.Errorf("%d isn't an even number", i)
    }
    return i * 2, nil
}
```

# errors are values

Since `error` is an interface, you can define your own that include additional information for logging or handling.

```go
type Status int

const (
    InvalidLogin Status = iota + 1
    NotFound
)

type StatusErr struct {
    Status Status
    Message string
}

func (se StatusErr) Error() string {
    return se.Message
}

func LoginAndGetData(uid, pwd, file string) ([]byte, error) {
    err := login(uid, pwd)

    if err != nil {
        return nil, StatusErr{
            Status: InvalidLogin,
            Message: fmt.Sprintf("invalid credentials for user %s", uid),
        }
    }

    data, err := getData(file)

    if err != nil {
        return nil, StatusErr{
            Status: NotFound,
            Message: fmt.Sprintf("file %s not found", file),
        }
    }

    return data, nil
}
```

Even when defining custom errors, always use `error` as the return type for the error result. 

# wrapping errors

Preserve an error while adding additional information - called *wrapping* the error. A series of wrapped errors is called an *error chain*

Use `fmt.Errorf` to wrap errors using the special verb, `%w`. Use this to create an error whose formatted string includes the formatted string of another error and which contains the original error as well.

The convention is to write `: %w` at the end of the error.

Unwrap errors with `errors.Unwrap`

```go
func fileChecker(name string) error {
    f, err := os.Open(name)

    if err != nil {
        return fmt.Errorf("in fileChecker: %w", err)
    }

    f.Close()

    return nil
}

func main() {
    err := fileChecker("not_here.txt")

    if err != nil {
        fmt.Println(err)
        if wrappedErr := errors.Unwrap(err); wrappedErr != nil {
            fmt.Println(wrappedErr)
        }
    }
}
```

# panic and recover

Go generates a panic whenever this is a situation where the Go runtime is unable to figure out what should happen next. As soon as a panic happens, the current function exits immediately and any defers attached start running and so on until main is reached. The program then exits with a message and a stack trace.

```go
func doPanic(msg string) {
    panic(msg)
}

func main() {
    doPanic(os.Args[0])
}
```

Use recover within a defer to allow execution to continue normally.

```go
func div60(i int) {
    defer func() {
        if v:= recover(); v != nil {
            fmt.Println(v)
        }
    }()

    fmt.Println(60 / i)
}

func main() {
    for _, val := range []int{1, 2, 0, 6} {
        div60(val)
    }
}
```

The pattern for using `recover`:

* Register a function with `defer` to handle a potential `panic`
* Call `recover` within an if statement and check to ese if a non-nil value was found
  * Must call `recover` from within a `defer` because once a `panic` happens, only deferred functions are run.

