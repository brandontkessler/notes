Whether or not to use concurrency depends on how data flows through the steps in the program. Step can be concurrent because the data from one is not required for the other to proceed, and other times whne steps must happen in a series because one is dependent on the other's output.

Also, it's not worth using concurrency if the process that's running doesn't take a lot of time.

Concurrent operations are often used for I/O

# goroutines

A process is an instance of a program that's being run by a computer's OS. The OS associates some resources (memory) to the process and ensures other processes can't access them. A process consists of one or more threads. A thread is a unit of execution that is given some time to run by the OS. Threads within a process share access to resources.

Goroutines are lightweight processes managed by the Go runtime. 

When a Go program starts, the Go runtime creates a number of threads and launches a single goroutine to run the program. All goroutines created by your program are assigned to threads automatically by the Go runtime scheduler.

* Goroutine creation is faster than thread creation
* Goroutine stack sizes are smaller than threads and can grow as needed making them more memory efficient
* Switching between goroutines is faster than switching between threads

# implementation

Place the `go` keyword before a function invocation.

It is best practice to use wrappers on the goroutines. For example, the closure reads values out of channels (channels are discussed in next section) and passes them to the business logic, which is completely unaware that it is running in a goroutine. The result is written back to a different channel.

```go
func process(val int) int {
    // do something with val
}

func runThingConcurrently(in <-chan int, out chan<- int) {
    go func() {
        for val := range in {
            result := process(val)
            out <- result
        }
    }()
}
```

# channels - reading, writing, and buffering

Goroutines communicate using channels. Like slices and maps, channels are created using `make`

```go
ch := make(chan int)
```

Use the <- operator to interact with a channel. You read from a channel with <- to the left of the variable and write to a channel by placing it to the right.

```go
a := <-ch // reads a value from ch and assigns it to a
ch <- b   // write the value in b to ch
```

Each value written to a channel can only be read once. 

When assigning a channel to a variable or field or passing it to a function, use an arrow before the `chan` keyword (`ch <- chan int`) to indicate that the goroutine only reads from the channel. `ch chan<- int` indicates it only writes to the channel.

Channels are unbuffered by default. If the buffer fills before there are any reads from teh channel, subsequent writes to the channel pause the writing goroutine until the channel is read. A buffered channel is created with:

```go
ch := make(chan int, 10)
```

# for-range and channels

```go
for v := range ch {
    fmt.Println(v)
}
```

There is only a single var declared for the channel, which is the value. The loop continues until the channel is closed or until a break or return statement is reached.

```go
close(ch) // closes a channel
v, ok := <-ch // if ok is true then the channel is open, if it false, the channel is closed
```

# select

The select statement is the control structure for concurrency solving for if you can perform two concurrent operations, which one do you do first?

```go
select {
    case v := <-ch:
        fmt.Println(v)
    case v := <-ch2:
        fmt.Println(v)
    case ch3 <- x:
        fmt.Println("wrote", x)
    case <-ch4:
        fmt.Println("got a value on ch4 but ignored it")
}
```

# deadlocks

The below func results in a deadlock and throws an error. This is because the goroutine we launch cannot proceed until ch2 is read, and the main goroutine cannot proceed until ch2 is read. 

```go
func main() {
    ch1 := make(chan int)
    ch2 := make(chan int)

    go func() {
        v := 1
        ch1 <- v
        v2 := <-ch2
        fmt.Println(v, v2)
    }()

    v := 2
    ch2 <- v
    v2 := <-ch1
    fmt.Println(v, v2)
}
```

If we wrap channel accesses in the main goroutine in a select, we avoid the deadlock

```go
func main() {
    ch1 := make(chan int)
    ch2 := make(chan int)

    go func() {
        v := 1
        ch1 <- v
        v2 := <-ch2
        fmt.Println(v, v2)
    }()

    v := 2
    var v2 int

    select {
        case ch2<- v:
        case v2 = <-ch1:
    }
    fmt.Println(v, v2)
}
```

Because a select checks if any of its cases can proceed, the deadlock is avoided. The goroutine we launched wrote the value 1 into ch1, so the read from ch1 into v2 in the main goroutine is able to succeed. 

Select is often embedded within a for loop, referred to as a for-select group.

```go
for {
    select {
        case <-done:
            return
        case v := <-ch:
            fmt.Println(v)
    }
}
```

We can also use a default clause. The following does not wait if there's no value to read in ch; it immediately executes the body of the default. **This is always the wrong thing to do**

```go
select {
    case v := <-ch:
        fmt.Println("read from ch:", v)
    default:
        fmt.Println("no value written to ch")
}
```

This will be triggered every time through the loop when there's nothing to read or write for any cases making the loop run constantly.

# goroutine cleanup

If the goroutine doesn't exit, the scheduler will still periodically give it time to do nothing called *goroutine leak*

The done channel pattern provides a way to stop.

```go
func searchData(s string, searchers []func(string) []string) []string {
    done := make(chan struct{})
    result := make(chan []string)

    for _, searcher := range searchers {
        go func(searcher func(string) []string) {
            select {
                case result <- searcher(s):
                case <-done:
            }
        }(searcher)
    }

    r := <- result
    close(done)
    return r
}
```

We declare a channel named done that contains data of type struct (the value is unimportant as we never write to this channel, we only close it). Launch a goroutine for each searcher passed in. The select statements on the worker goroutine waits for either a write on the result channel (when the searcher func returns) or a read on the done channel. 

The vase that reads from done will stay paused until done is closed. 

In searchData we read the first value written to result, and then we close done. This signals to the goroutines that they should exit. 

# cancel to terminate a goroutine

```go
func countTo(max int) (<-chan int, func()) {
    ch := make(chan int)
    done := make(chan struct{})
    cancel := func() {
        close(done)
    }

    go func() {
        for i := 0; i < max; i++ {
            select {
                case <-done:
                    return
                default:
                    ch <- i
            }
        }
        close(ch)
    }()
    return ch, cancel
}

func main() {
    ch, cancel := countTo(10)
    for i := range ch {
        if i > 5 {
            break
        }
        fmt.Println(i)
    }
    cancel()
}
```

# when to use buffered and unbuffered channels

Proper use of a buffered channel means you must handle the case where the buffer is full and your writing goroutine blocks waiting for a reading goroutine. 

Buffered channels are useful when you know how many goroutines you have launched, want to limit the number of goroutines you will launch, or want to limit the amount of work that is queued up.

Buffered channels work great when you want to either gather data back from a set of goroutines that you have launched or when you want to limit concurrent usage. 

```go
func processChannel(ch chan int) []int {
    const conc = 10

    results := make(chan int, conc)

    for i := 0; i < conc; i++ {
        go func() {
            results <- process(v)
        }()
    }

    var out []int
    for i := 0; i < conc; i++ {
        out = append(out, <-results)
    }

    return out
}
```

# turning off a case in a select

```go
for {
    select {
        case v, ok := <-in:
            if !ok {
                in = nil // the case will never succeed again when set to nil
                continue
            }
            // process the v that was read from in
        case v, ok := <-in2:
            if !ok {
                in2 = nil
                continue
            }
            // process the v that was from in2
        case <-done:
            return
    }
}
```

# using WaitGroups

Sometimes one goroutine needs to wait for multiple goroutines to complete their work. 

```go
func main() {
    var wg sync.WaitGroup
    wg.Add(3)

    go func() {
        defer wg.Done()
        doThing1()
    }()

    go func() {
        defer wg.Done()
        doThing2()
    }()

    go func() {
        defer wg.Done()
        doThing3()
    }()

    wg.Wait()
}
```

WaitGroup.Add - increments the counter of goroutines to wait for. Done decrements the counter and is called by a goroutine when it is finished. Wait pauses its goroutine until the counter hits zero. 

```go
func processAndGather(in <-chan int, processor func(int) int, num int) []int {
    out := make(chan int, num)
    var wg sync.WaitGroup
    wg.Add(num)

    for i :=0; i < num; i++ {
        go func() {
            defer wg.Done()
            for v := range in {
                out <- processor(v)
            }
        }()
    }

    go func() {
        wg.Wait()
        close(out)
    }()

    var result []int

    for v := range out {
        result = append(result, v)
    }

    return result
}
```

Use WaitGroups only when you have something to clean up after all of your worker goroutines exit. 

# putting concurrent tools together

We have a function that calls three web services. We send data to two of those services, and then take the results of those two calls and send them to the third, returning the result. The entire process must take less than 50ms or an error is returned. 

```go
func GatherAndProcess(ctx context.Context, data Input) (COut, error) {
    ctx, cancel := context.WithTimeout(ctx, 50*time.Millisecond)
    
    defer cancel()

    p := processor{
        outA: make(chan AOut, 1),
        outB: make(chan BOut, 1),
        inC:  make(chan Cin, 1),
        outC: make(chan COut, 1),
        errs: make(chan error, 2),
    }

    p.launch(ctx, data)

    inputC, err := p.waitForAB(ctx)

    if err != nil {
        return COut{}, err
    }

    p.inC <- inputC

    out, err := p.waitForC(ctx)

    return out, err
}
```

Set up context that times out in 50ms. The done method on the context returns a channel that returns a value when the context is cancelled, either by timing out or by calling the context's cancel method.

Populate the processor instance with a series of channels that we'll use to communicate with our goroutines. Every channel is buffered so that the goroutines that write to them can exit after writing without waiting for a read to happen. Errors could potentially have 2 errors written to it. 

```go
type processor struct {
    outA chan AOut
    outB chan BOut
    outC chan COut
    inC  chan CIn
    errs chan error
}
 
func (p *processor) launch(ctx context.Context, data Input) {
    go func() {
        aOut, err := getResultA(ctx, data.A)

        if err != nil {
            p.errs <- err
            return
        }

        p.outA <- aOut
    }()

    go func() {
        bOut, err := getResultB(ctx, data.B)

        if err != nil {
            p.errs <- err
            return
        }

        p.outB <- bOut
    }()

    go func() {
        select {
            case <-ctx.Done():
                return
            case inputC := <-p.inC:
                cOut, err := getResultC(ctx, inputC)

                if err != nil {
                    p.errs <- err
                    return
                }

                p.outC <- cOut
        }
    }()
}
```

The goroutines for getResultA and getResultB are very similar. They call their respective methods and handle an error if one is returned. If a valid value is returned, they write to their channels. Since the call to getResultC only happens if the calls to getResultA and getResultB succeed and happen within 50ms, the third goroutine is slightly more complicated.

```go
func (p *processor) waitForAB(ctx context.Context) (CIn, error) {
    var inputC Cin
    count := 0

    for count < 2 {
        select {
            case a := <-p.outA:
                inputC.A = a
                count++
            case b := <-p.outB:
                inputC.B = b
                count++
            case err := <-p.errs:
                return CIn{}, err
            case <-ctx.Done():
                return CIn{}, ctx.Err()
        }
    }
    return inputC, nil
}
```

This uses for-select to populate inputC, an instance of CIn, the input parameter for getResultC. If both of the first two cases execute, we exit the for-select loop and return the value of inputC and a nil error.

The second two cases handle error conditions. If an error was written to p.errs channel, we return the error. The context has been cancelled.

If all is well, we write the inputC value to the p.inC channel and then call the waitForC method on processor:

```go
func (p *processor) waitForC(ctx context.Context) (Cout, error) {
    select {
        case out := <- p.outC:
            return out, nil
        case err := <-p.errs:
            return COut{}, err
        case <-ctx.Done():
            return COut{}, ctx.Err()
    }
}
```

If getResultC completed successfully, we read its output from the p.outC channel and return it. 
