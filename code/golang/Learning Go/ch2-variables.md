# floats

Due to floats being estimated (inexact), don't use == or != for comparisons. Instead, subtract the values from eachother and define a minimum allowed variance.

# variable assignment

multi-variable assignment

```go
var (
    x    int
    y        = 20
    z    int = 30
    d, e     = 40, "hello"
    f, g string
)
```

when initializing a variable to its zero value, use `var x int`, this makes it clear the zero value is intended. 

# constants

Constants can be typed or untyped. Untyped constants can be assigned to other typed variables

```go
const x = 10

var y int = x
var z float64 = x

// ##### 

const a int = 10
var b float64 = a // this will fail
```

# naming

Go does not use all upper case letters like INDEX_COUNTER for package level vars. It only uses the first letter being capital to determine if it should be accessible outside of the package: IndexCounter.

Within functions, favor short variable names (the smaller the scope the shorter the name). It is common to use k and v for key, value loops or i and j for indexing.

