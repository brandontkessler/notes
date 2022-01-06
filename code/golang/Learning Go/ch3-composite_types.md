# arrays

```go
var x = [12]int{1, 5: 4, 6, 10:100, 15}

// index 5 is 4 and index 10 is 100. Anything else is the zero val which is 0 for int
// results in [1 0 0 0 0 4 6 0 0 100 15]

var x = [...]int{1, 2, 3}
var y = [3]int{1, 2, 3}
fmt.Println(x == y) // prints true

// access multi-dimensional arrays:
var x [2][3]int // array of length 2 whose type is an array of ints of length 3
```

In general, don't use arrays unless you know the exact length ahead of time. Use slices instead.

# slices

```go
var x []int
x = append(x, 10, 5, 7)
// [10 5 7]

// slices can be appended to eachother using ... operator
y := []int{20,30,40}
x = append(x, y...)
```

Slices grow capacity automatically but it's far more efficient to size them once. Do this using the `make` function.

```go
x := make([]int, 5) 
// creates an int slice with length 5 and capacity 5 initialized to 0
x = append(x, 10) // [0 0 0 0 0 10]

x := make([]int, 5, 10) // slice of length 5 initialized to 0 and capacity of 10
x = append(x, 5, 6, 7, 8) // [0 0 0 0 0 5 6 7 8]

data := []int{2, 4, 6, 8} // slice literal
```

If you know how large your slice needs to be, but don't know what those values will be when you are writing the program, use `make`. 

Preferences are split, but in general intialize a slice with zero len and use append. It may be slower but is less likely to introduce a bug.

Use `:` to slice slices

```go
x := []int{1, 2, 3, 4}
y := x[:2]  // [1 2]
d := x[1:3] // [2 3]
e := x[:]   // [1 2 3 4]
```

Slices of slices share memory w/ the original meaning that if you make a change to the slice of a slice, it will also change the original slice.

To avoid confusion, never use append with a sub-slice.

To create a slice that is independent of the original, us `copy`. It takes 2 params, the first is the destination slice, the second is the source slice copying whatever it can limited to the smaller slice. 

```go
x := []int{1,2,3,4}
y := make([]int, 4)
num := copy(y, x)
fmt.Println(y, num) // [1 2 3 4] 4
```

# strings

Strings can be treated similarly to slices using indexing and subslices of strings. Go uses a sequence of bytes to represent a string.

```go
var s string = "Hello there"
var b byte = s[6]
var s2 string = s[4:7]  // "o t"
var s3 string = s[:5]   // "Hello"
```

Due to type conversions issues between strings, bytes, and runes, use functions from the `strings` package to get substrings from strings.

# maps

```go
teams := map[string][]string
{
    "Orcas": []string{"Fred", "Ralph", "Bijou"},
    "Lions": []string{"Sarah", "Peter", "Billie"}
}

// use `make` to create a map with a default size:
ages := make(map[int][]string, 10)



totalWins := map[string]int{}
totalWins["Orcas"] = 1
totalWins["Lions"] = 2
totalWins["Kittens"]++

fmt.Println(totalWins["Orcas"])     // 1
fmt.Println(totalWins["Lions"])     // 2
ftm.Println(totalWins["Kittens"])   // 1


// comma ok idiom returns bool
v, ok := totalWins["Lions"] // ok returns true
v, ok := totalWins["Dogs"]  // ok returns fals

// Use delete function to delete key from map
m := map[string]int{
    "hello": 4,
    "world": 6
}
delete(m, "hello")
```

# structs

Maps are convenient for storing data but don't define an API since there's no way to constrain a map to only allow certain keys. All values of a map must also be of the same type. This makes maps not the ideal way to pass data from function to function. Use structs instead.

```go
type person struct {
    name    string
    age     int
    pet     string
}

var fred person
bob := person{}

julia := person{
    "Julia",
    40,
    "cat",
}

beth := person{
    age: 30,
    name: "Beth",
}

bob.name = "Bob"

fmt.Println(beth.name)
```

## anonymous structs

You can also declare a variable implements a struct type without first iving the struct type a name.

```go
var person struct {
    name    string
    age     int
    pet     string
}

person.name = "bob"
person.age = 50

pet := struct {
    name    string
    kind    string
}{
    name: "Fido",
    kind: "dog",
}
```

When is this useful?

1. translating external data into a struct or a struct into external data (JSON) - This is called *marshaling* and *unmarshaling* data.
2. Writing tests


