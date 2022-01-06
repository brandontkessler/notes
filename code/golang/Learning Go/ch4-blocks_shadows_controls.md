# shadow vars

Occur when the same variable is named in different blocks

```go
person := "bob"
if <conditional>{
    person := "dave"
}

fmt.Println(person) // returns bob
```

To ensure there is no shadow vars add the shadow linter:

```bash
$ go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow@latest
```

Add it to the vet of the Makefile:

```
vet:
    go vet ./...
    shadow ./...
.PHONY: vet
```

# for loops - 4 ways

1. the complete for loop
2. the condition-only for loop
3. the infinite for loop (while statement)
4. for range for loop


```go
// the complete for loop
for i := 0; i < 10; i++ {
    fmt.Println(i)
}


// the condition only for loop
i := 1
for i < 100 {
    fmt.Println(i)
    i = i * 2
}


// the infinite for loop
for {
    fmt.Println("Hello")
}


// for-range statement
evenVals := []int{2, 4, 6, 8, 10, 12}
for i, v := range evenVals {
    fmt.Println(i, v)
}
```

You will typically only use the complete for loop and the for range statement

Use the complete for loop when not iterating from the first elemnt to the last element in a compound type. You can get away with using if, continue, and break in a for-range loop but the standard for loop is much clearer. See below example.

```go
// using for range with conditionals
evenVals := []int{2, 4, 6, 8, 10}
for i, v := range evenVals {
    if i == 0 {
        continue
    }
    if i == len(evenVals)-2 {
        break
    }
    fmt.Println(i, v)
}

// using the complete for loop but with same result as above
evenVals := []int{2, 4, 6, 8, 10}
for i := 1; i < len(evenVals)-1; i++ {
    fmt.Println(i, evenVals[i])
}
```

# switch

```go
words := []string{"a", "cow", "smile", "gopher", "octopus", "anthropologies"}

for _, word := range words {
    switch size := len(word); size {
        case 1, 2, 3, 4:
            fmt.Println(word, "is a short word!")
        case 5:
            wordLen := len(word)
            fmt.Println(word, "is exactly the right length:", wordLen)
        case 6, 7, 8, 9:
        default:
            fmt.Println(word, "is a long word!")
    }
}

// The output from above is:

// a is a short word!
// cow is a short word!
// smile is exactly the right length: 5
// anthropologies is a long word!
```

If nothing included as the case, nothing happens

In the case that you are using a switch statement to break out of a for loop, you must a put a label on the break statement, otherwise, it thinks you're breaking out of the case statement.

```go
for i := 0; i < 10; i++ {
    switch {
        case i%2 == 0:
            fmt.Println(i, "is even")
        case i%3 == 0:
            fmt.Println(i, "is divisible by 3 but not 2")
        case i%7 == 0:
            fmt.Println("exit the loop!")
            break
        default:
            fmt.Println(i, "is boring")
    }
}
// This will not break out of the loop, it will break out of that
// particular case and move to the next in the for loop. Instead, use
// break loop
for i := 0; i < 10; i++ {
    switch {
        case i%2 == 0:
            fmt.Println(i, "is even")
        case i%3 == 0:
            fmt.Println(i, "is divisible by 3 but not 2")
        case i%7 == 0:
            fmt.Println("exit the loop!")
            break loop // here
        default:
            fmt.Println(i, "is boring")
    }
}
```

## blank switches

A regular switch only allows you to check a value for equality. A blank switch allows you to use any boolean comparison for each case.

```go
words := []string{"hi", "salutations", "hello"}

for _, word := range words {
    switch wordLen := len(word); {
        case wordLen < 5:
            fmt.Println(word, "is a short word")
        case wordLen > 10:
            fmt.Println(word, "is a long word")
        default:
            fmt.Println(word, "is a good word")
    }
}
```

If all cases are equality comparisons against the same variable, don't use blank switch:

```go
// Don't do this!
switch {
    case a == 2:
        fmt.Println("a is 2")
    case a == 3:
        fmt.Println("a is 3")
    case a == 4:
        fmt.Println("a is 4")
    default:
        fmt.Println("a is something else")
}

// Do this instead:
switch a {
    case 2:
        fmt.Println("a is 2")
    case 3:
        fmt.Println("a is 3")
    // etc
}
```

## choosing between if an switch

If all comparison are related use switch, if comparisons are unrelated use if else or consider refactoring code.

# goto

Chances are you will never use this.

```go
func main() {
    a := rand.Intn(10)

    for a < 100 {
        if a%5 == 0 {
            goto done
        }
        a = a*2 + 1
    }
    fmt.Println("do something when the loop completes normally")

    done:
        fmt.Println("do complicated stuff no matter why we left the loop")
        fmt.Println(a)
}
```

