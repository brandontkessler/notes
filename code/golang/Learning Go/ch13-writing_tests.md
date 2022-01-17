# example

Every test is written in a file whose name ends with _test.go.

Test function starts with the word `Test`

```go
func addNumbers(x, y int) int {
    return x + x
}
```

The corresponding test is:

```go
import (
    "testing"
)

func Test_addNumbers(t *testing.T) {
    result := addNumbers(2,3)

    if results != 5 {
        t.Error("incorrect result: expected 5, got", result)
    }
}
```

Then run:

```bash
$ go test
```

The `go test` command allows you to specify which packages to test. Using `./...` for the package name specifies that you want to run tests in the current directory and all of the subdirectories of the current directory. Include a `-v` flag to get verbose.

# reporting test failures

* `t.Error`
* `t.Errorf` - similar to Printf
  * `t.Errorf("incorrect result: expected %d, got %s", 5, result)`
* `t.Fatal` and `t.Fatalf` will stop the test function processing as soon as a failure is found. With `t.Error` and `t.Errorf` it will continue processing even if found an error.
  * If a check in a test is dependent on another check passing, use fatal in the first.

# setting up and tearing down

Use a `TestMain` functio nto manage state and run your tests:

```go
var testTime time.Time

func TestMain(m *testing.M) {
    fmt.Println("Set up stuff for tests here")
    testTime = time.Now()
    exitVal := m.Run()
    fmt.Println("Clean up stuff after tests here")
    os.Exit(exitVal)
}

func TestFirst(t *testing.T) {
    fmt.Println("TestFirst uses stuff set up in TestMain", testTime)
}

func TestSecond(t *testing.T) {
    fmt.Println("TestSecond also uses stuff set up in TestMain", testTime)
}
```

Just like `defer`, the `Cleanup` functions are invoked in last added, first called order.

```go
// createFile is a helper function called from multiple tests
func createFile(t *testing.T) (string, error) {
    f, err := os.Create("tempFile")
    if err != nil {
        return "", err
    }

    // write some data to f
    t.Cleanup(func() {
        os.Remove(f.Name())
    })

    return f.Name(), nil
}

func TestFileProcessing(t *testing.T) {
    fName, err := createFile(t)
    if err != nil {
        t.Fatal(err)
    }

    // do testing, don't worry about cleanup
}
```

# storing sample test data

Create a subdirectory named `testdata` to hold your files. Go reserves this directory name as a place to hold test files. **When reading from `testdata` always use a relative file reference.** This is because `go test` changes the current working directory to the current package as it walks through the code. Each package accesses its own `testdata` via a relative file path.

# test packages

Instead of keeping tests as part of the same package, create a new package for tests using the `packagename_test` naming convention. 

For example, given a package named `adder`:

```go
func AddNumbers(x, y int) int {
    return x + y
}
```

We can test it as a public API using the following colde in a file in the `adder` package named `adder_public_test.go`:

```go
package adder_test

import (
    "testing"
    "test_examples/adder"
)

func TestAddNumbers(t *testing.T) {
    result := adder.AddNumbers(2, 3)
    if result != 5 {
        t.Error("incorrect result: expected 5, got", result)
    }
}
```

# use go-cmp to compare test results

Google released a third party module called go-cmp that does comparisons for you and returns detailed descriptions of what does not match. Module at [github.com/google/go-cmp/cmp](github.com/google/go-cmp/cmp)

Example below by defining a `struct` and a factory function that populates it.

```go
type Person struct {
    Name        string
    Age         int
    DateAdded   time.Time
}

func CreatePerson(name string, age int) Person {
    return Person {
        Name: name,
        Age: age,
        DateAdded: time.Now(),
    }
}
```

In our test file, we need to import `github.com/google/go-cmp/cmp` and our test function looks like this:

```go
func TestCreatePerson(t *testing.T) {
    expected := Person{
        Name: "Dennis",
        Age: 38,
    }

    result := CreatePerson("Dennis", 38)

    if diff := cmp.Diff(expected, result); diff != "" {
        t.Error(diff)
    }
}
```

The cmp.Diff function takes in the expected output, and the output that was returned by the function that we're testing. If the inputs match, it returns an empty string. 

The output lines with a - and + indicate the fields whose values differ. In this case, the test fails because the dates won't match. This is because we can't control what date is assigned by the CreatePerson function. To ignore the field, specify a comparator function. Declare the function as a local variable in the test.

```go
comparer := cmp.Comparer(func(x, y Person) bool {
    return x.Name == y.Name && x.Age == y.Age
})

if diff := cmp.Diff(expected, result, comparer); diff != "" {
    t.Error(diff)
}
```

# table tests

Use this pattern to test more than a single case at a time instead of writing multiple test functions. Given the following function:

```go
func DoMath(num1, num2 int, op string) (int, error) {
    switch op {
        case "+":
            return num1 + num2, nil
        case "-":
            return num1 - num2, nil
        case "*":
            return num1 * num2, nil
        case "/":
            if num2 == 0 {
                return 0, errors.New("division by zero")
            }
            return num1 / num2, nil
        default:
            return 0, fmt.Errorf("unknown operator %s", op)
    }
}
```

To test this, we need to check all of the cases. 

**Don't do:**

```go
func TestDoMath(t *testing.T) {
    result, err := DoMath(2, 2, "+")
    if result != 4 {
        t.Error("Should have been 4")
    }
    // and so on...
}
```

**Do this with a table test instead**

First, declare a slice of anonymous structs. The struct contains fields for the name of the test, the input parameters, and the return values. Each entry in the slice represents another test:

```go
data := []struct {
    name    string
    num1    int
    num2    int
    op      string
    expected int
    errMsg  string
}{
    {"addition", 2, 2, "+", 4, ""},
    {"subtraction", 2, 2, "-", 4, ""},
    {"multiplication", 2, 3, "*", 6, ""},
    {"division", 2, 2, "/", 1, ""},
    {"bad_division", 2, 0, "/", 0, `division by zero`},
}
```

Then loop over each test case in data, invoking the Run method each time. When you do the -v flag on the `go test`, each subtest now has a name:

```go
for _, d := range data {
    t.Run(d.name, func(t *testing.T) {
        result, err := DoMatch(d.num1, d.num2, d.op)

        if result != d.expected {
            t.Errorf("Expected %d, got %d", d.expected, result)
        }

        var errMsg string

        if err != nil {
            errMsg = err.Error()
        }

        if errMsg != d.errMsg {
            t.Errorf("Expected error message `%s`, got `%s`", d.errMsg, errormsg)
        }
    })
}
```

Comparing error messages can be fragile...

# checking code coverage

Use:

```bash
$ go test -v cover -coverprofile=c.out
$ go tool cover -html=c.out
```

# benchmarks

The below fun, counts the number of characters in a file. It takes the name of the file and the size of the buffer.

```go
func FileLen(f string, bufsize int) (int, error) {
    file, err := os.Open(f)

    if err != nil {
        return 0, err
    }

    defer file.Close()

    count := 0
    for {
        buf := make([]byte, bufsize)
        num, err := file.Read(buf)
        count += num

        if err != nil {
            break
        }
    }

    return count, nil
}
```

Before benchmarking, let's test it to make sure it works:

```go
func TestFileLen(t *testing.T) {
    result, err := FileLen("testdata/data.txt", 1)
    if err != nil {
        t.Fatal(err)
    }

    if result != 65204 {
        2 t.Error("Expected 65204, got", result)
    }
}
```

Now check benchmarking. Benchmarks are functions in test files that start with the word Benchmark and take in a single parameter of type `*testing.B`.  Let's start by benchmarking a buffer size of 1 byte

```go
var blackhole int

func BenchmarkFileLen(b *testing.B) {
    for i := 0; i < b.N; i++ {
        result, err := FileLen("testdata/data.txt", 1)
        if err != nil {
            b.Fatal(err)
        }
        blackhole = result
    }
}
```

Every benchmark must have a loop that iterates from 0 to b.N. The testing framework calls our benchmark functions over and over until it is sure that the timing results are accurate. 

Run the benchmark by passing the `-bench` flag to `go test`. The flag expects a regex to describe the name of the benchmarks to run. Use `-bench=.` to run all benchmarks.

A second flag `-benchmem` includes memory allocation information in the output. A sample output looks like:

> BenchmarkFileLen1-12 25 47201025 ns/op 65342 B/op 65208 allocs/op

Here's what each column means:

* BenchmarkFileLen1-12: The name of the benchmark, a hyphen, and the value of GOMAXPROCS for the benchmark
* 25: The number of times the test ran in order to produce a stable result
* 47201025 ns/op: How long it took to run a single pass of this benchmark in nanoseconds (1B nanoseconds per second)
* 65342 B/op: The number of bytes allocated during a single pass of the benchmark
* 65208 allocs/op: The number of times bytes had to be allocated from the heap during a single pass of the benchmark. This will always be less or equal to the number of bytes allocated

Let's check results when using buffers of different sizes:

```go
func BenchmarkFileLen(b *testing.B) {
    for _, v := range []int{1, 10, 100, 1000, 10000, 1000000} {
        b.Run(fmt.Springf("FileLen-%d", v), func(b *testing.B) {
            for i := 0; i < b.N; i++ {
                result, err := FileLen("testdata/data.txt", v)

                if err != nil {
                    b.Fatal(err)
                }

                blackhole = result
            }
        })
    }
}
```

Just like we launched table tests using `t.Run` we're using `b.Run` to launch benchmarks that only vary based on input. 

# stubs

Most code is dependent on other code. There are two ways that Go allows us to abstract function calls: defining a function type and defining an interface. These abstractions not only help us write modular production code, they also help us write unit tests.

Example:

```go
type Processor struct {
    Solver MathSolver
}

type MathSolver interface {
    Resolve(ctx context.Context, expression string) (float64, error)
}
```

We'll implement and test `MathSolver` in a bit. `Processor` also has a method that reads an expression from an `io.Reader` and returns the calculated value:

```go
func (p Processor) ProcessExpression(ctx context.Context, r io.Reader) (float64, error) {
    curExpression, err := readToNewLine(r)

    if err != nil {
        return 0, err
    }

    readToNewLine(r)

    if err != nil {
        return 0, err
    }

    if len(curExpression) == 0 {
        return 0, errors.New("no expression to read")
    }

    answer, err := p.Solver.Resolve(ctx, curExpression)

    return answer, err
}
```

To test `ProcessExpressions`, first we need a simple implementation of the `Resolve` method:

```go
type MathSolverStub struct{}

func (ms MathSolverStub) Resolve(ctx context.Context, expr string) (float64, error) {
    switch expr {
        case "2 + 2 * 10":
            return 22, nil
        case "( 2 + 2 ) * 10":
            return 40, nil
        case "( 2 + 2 * 10":
            return 0, errors.New("invalid expression: ( 2 + 2 * 10")
    }
    return 0, nil
}
```

Next write a unit test that uses this stub (production code should test the error message too).

```go
func TestProcessorProcessExpressions(t *testing.T) {
    p := Processor{MathSolverStub{}}
    
    in := strings.NewReader(`2 + 2 * 10
    ( 2 + 2 ) * 10
    ( 2 + 2 * 10`)

    data := []float64{22, 40, 0, 0}

    for _, d := range data {
        result, err := p.ProcessExpression(context.Background(), in)

        if err != nil {
            t.Error(err)
        }

        if result != d {
            t.Errorf("Expected result %f, got %f", d, result)
        }
    }
}
```

There are times when an interface may specify multiple methods. Take for example:

```go
type Entities interface {
    GetUser(id string) (User, error)
    GetPets(userID string) ([]Pet, error)
    GetChildren(userID string) ([]Person, error)
    GetFriends(userID string) ([]Person, error)
    SaveUser(user User) error
}
```

In this case, a solution is to create a stub struct that proxies method calls to function fields. For each method defined on `Entities`, we define a function field with a matching signature on our stub struct:

```go
type EntitiesStub struct {
    getUser     func(id string) (User, error)
    getPets     func(userID string) ([]Pet, error)
    getChildren func(userID string) ([]Person, error)
    getFriends  func(userID string) ([]Person, error)
    saveUser    func(user User) error
}
```

Then, make `EntitiesStub meet the `Entities interface by defining the methods. In each method, we invoke the associated function field. For example:

```go
func (es EntitiesStub) GetUser(id string) (User, error) {
    retrn es.getUser(id)
}

func (es EntitiesStub) GetPets(userID string) ([]Pet, error) {
    return es.getPets(userID)
}
```

Once you create this stub, you can supply different implementations of different methods in different test cases via the fields in the data struct for a table test:

```go
func TestLogicGetPetNames(t *testing.T) {
    data := []struct {
        name        string
        getPets     func(userID string) ([]Pet, error)
        userID      string
        petNames    []string
        errMsg      string
    }{
        {
            "case1", 
            func(userID string) ([]Pet, error) {
                return []Pet{{Name: "Bubbles"}}, nil
            }, 
            "1", 
            []string{"Bubbles"}, 
            ""
        }, {
            "case2", 
            func(userID string) ([]Pet, error) {
                return nil, errors.New("invalid id: 3")
            }, 
            "3", 
            nil, 
            "invalid id: 3"
        }, 
    }

    l := Logic{}
    for _, d := range data {
        t.Run(d.name, func(t *testing.T) {
            l.Entities = EntitiesStub{getPets: d.getPets}

            petNames, err := l.GetPetNames(d.userID)

            if diff := cmp.Diff(petNames, d.petNames); diff != "" {
                t.Error(diff)
            }

            var errMsg string

            if err != nil {
                errMsg = err.Error()
            }

            if errMsg != d.errMsg {
                t.Errorf("Expected error `%s`, got `%s`", d.errMsg, errMsg)
            }
        })
    }
}
```