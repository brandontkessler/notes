# arrays

arrays are zero indexed
array's length is part of its type so it cannot be resized

```go
var a [10]int // declares variable `a` as an array of 10 integers

func main() {
    primes := [6]int{2, 3, 5, 7, 11, 13}
}
```

## Slices

* references to arrays and do not store any data.
* dynamically-sized, flexible view into the elements of an array.
* `a[low : high]` selects half-open range which includes the first element but excludes the last.
* omit low or high bounds to use defaults: 0 is low, len slice is high
  * `a[0:10] == a[:10]`

```go
func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}

	var s []int = primes[1:4]
	fmt.Println(s) // returns [3, 5, 7]

    s[0] = 10
    fmt.Println(s) // returns [10, 5, 7]
    fmt.Println(primes) // returns [2, 10, 5, 7, 11, 13]
}
```

---

### Slice literals

like an array literal but without length:

```go
[3]bool{true, true, false}  // array literal
[]bool{true, true, false}   // slice literal
```

---

## Slice length and capacity

* length: number of elements contained
* capacity: number of elements in underlying array

```go
func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	printSlice(s) // len=6 cap=6 [2 3 5 7 11 13]

	// Slice the slice to give it zero length.
	s = s[:0]
	printSlice(s) // len=0 cap=6 []

	// Extend its length.
	s = s[:4]
	printSlice(s) // len=4 cap=6 [2 3 5 7]

	// Drop its first two values.
	s = s[2:]
	printSlice(s) // len=2 cap=4 [5 7]
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```

---

The zero value of a slice is nil

```go
func main() {
	var s []int
	fmt.Println(s, len(s), cap(s))
	if s == nil {
		fmt.Println("nil!")
	}
}
```

---

## Creating slices with make

`make` allocates a zeroed array and returns a slice referring to it

```go
a := make([]int, 5)         // len(a) = 5
b := make([]int, 5, 10)     // len(b) = 5, cap(b) = 10

```

---

Slices can contain any type including other slices

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// Create a tic-tac-toe board.
	board := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

	// The players take turns.
	board[0][0] = "X"
	board[2][2] = "O"
	board[1][2] = "X"
	board[1][0] = "O"
	board[0][2] = "X"

	for i := 0; i < len(board); i++ {
		fmt.Printf("%s\n", strings.Join(board[i], " "))
	}
}
```

## Appending to a slice

The first parameter of append is a slice
The rest are values to append to the slice
If the backing array is too small to fit all the given values, a bigger array will be allocated.

```go
package main

import "fmt"

func main() {
	var s []int
	printSlice(s)

	// append works on nil slices.
	s = append(s, 0)
	printSlice(s)

	// The slice grows as needed.
	s = append(s, 1)
	printSlice(s)

	// We can add more than one element at a time.
	s = append(s, 2, 3, 4)
	printSlice(s)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```