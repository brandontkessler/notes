# conditionals

## if

```go
func main() {
    x := 10
	if x < 10 {
        fmt.Println("small")
	}
	fmt.Println("large")
}
```

--- 

`if` statements can start with short executionals before the condition
variables declared by the statement are only in scope until end of the if

```go
// This is the same as the function above
func main() {
	if x := 10; x < 10 {
        fmt.Println(x)
        fmt.Println("small")
	}

    // fmt.Println(x) -- cannot use `x` here
	fmt.Println("large")
}

func main() {
	if x := 10; x < 10 {
        fmt.Println(x)
        fmt.Println("small")
	} else {
        fmt.Println(x)
        fmt.Println("large")
    }
    // cannot use `x` here
}
```

## switch

shorter than `if - else`

```go
import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.\n", os)
	}
}
```

if no condition is passed, this is the same as `switch true`
cleaner way to write long if-then-else chains

```go
// same as above function
func main() {
	fmt.Print("Go runs on ")
	switch {
	case runtime.GOOS == "darwin":
		fmt.Println("OS X.")
	case runtime.GOOS == "linux":
		fmt.Println("Linux.")
	default:
		fmt.Printf("%s.\n", runtime.GOOS)
	}
}
```