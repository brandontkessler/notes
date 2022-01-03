# loops

Go only uses the `for` loop

1. init statement: executed before first iteration
2. condition expression: evaluated before every iteration
3. post statement: executed at the end of every iteration

The init and post statements are optional.

```go
func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

---

Since init and post are optional, you can emulate a `while` loop by 
excluding them.

```go
func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```