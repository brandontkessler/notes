* *abstract type* is one that specifies what a type should do but not how it is done. 
* *concrete type* specifies how and what. It has a specified way to store its data and provides an implementation of any methods declared on the type.

# methods

Methods for a type are defined at the package block level

```go
type Person struct {
    FirstName string
    LastName string
    Age int
}

func (p Person) String() string {
    return fmt.Sprintf("%s %s, age %d", p.FirstName, p.LastName, p.Age)
}

p := Person {
    FirstName: "Fred",
    LastName: "Fredson",
    Age; 52
}

output := p.String()
```

Methods look just like functions but have the added *receiver* specification. By convention, the receiver name is a short abbreviation of the type's name, usually its first letter. **It is non-idiomatic to us `this` or `self`**

# pointer receivers and value receivers

The following rules help determine when to use which:

* If your method modifies the receiver, you *must* use a pointer receiver.
* If your method needs to handle `nil` insteances, it must use a pointer receiver.
* If your method doesn't modify you can use a value receiver.

```go
type Counter struct {
    total int
    lastUpdated time.Time
}

func (c *Counter) Increment() {
    c.total++
    c.lastUpdated = time.Now()
}

func (c Counter) String() string {
    return fmt.Sprintf("total: %d, last updated: %v", c.total, c.lastUpdated)
}

var c Counter
fmt.Println(c.String())
c.Incrememnt()
fmt.Println(c.String())
```

# handle for nil instances

Sample code for a binary tree

```go
type IntTree struct {
    val         int
    left, right *IntTree
}

func (it *IntTree) Insert(val int) *IntTree {
    if it == nil {
        return &IntTree{val: val}
    }

    if val < it.val {
        it.left = it.left.Insert(val)
    } else if val > it.val {
        it.right = it.right.Insert(val)
    }

    return it
}

func (it *IntTree) Contains(val int) bool {
    switch {
        case it == nil:
            return false
        case val < it.val:
            return it.left.Contains(val)
        case val > it.val:
            return it.right.Contains(val)
        default:
            return true
    }
}

func main() {
    var it *IntTree
    it = it.Insert(5)
    it = it.Insert(3)
    it = it.Insert(10)
    it = it.Insert(2)
    fmt.Println(it.Contains(2)) // true
    fmt.Println(it.Contains(12)) // false

    fmt.Println(it.val)
	fmt.Println(it.left.val)
	fmt.Println(it.right.val)
	fmt.Println(it.left.left.val)
}
```

# iota

Iota is used to enumerate. Only the first constant needs to be set to iota, all following will automatically be assigned based on the enumeration.

```go
const (
	MailCategory = iota
	Personal
	Spam
	Social
	Advertisements
)

func main() {
	fmt.Println(MailCategory) // 0
	fmt.Println(Personal) // 1
	fmt.Println(Advertisements) // 4
}
```

# interfaces

The interface lists the methods that must be implemented by a concrete type to meet the interface. The methods defined by an interface are called the method set of the interface. 

They are usually named with "er" endings like `io.Reader`, `json.Marshaler`, etc.

```go
type LogicProvider struct {}

func (lp LogicProvider) Process(data string) string {
    // business logic
}

type Logic interface {
    Process(data string) string
}

type Client struct {
    L Logic
}

func (c Client) Program() {
    // get data from somewhere
    c.L.Process(data)
}

func main() {
    c := Client{
        L: LogicProvider{},
    }
    c.Program()
}
```

There is an interface, but only the caller (Client) knows about it; there is nothing declared on `LogicProvider` to indicate that it meets the interface. This is sufficient to both allow a new logic provider in the future as well as provide executable documentation to ensure that any type passed in to the client will match the client's need. 

# empty interface

One common use is as a placeholder for data of uncertain schemas read from external sources like json

```go
// One set of braces for the interface{} type, the other to instantiate
// an instance of the map
data := map[string]interface{}{}
contents, err := ioutil.ReadFile("testdata/sample.json")

if err != nil {
    return err
}

defer contents.close()

json.Unmarshal(contents, &data)
```

# type assertions and type switches

Assertions can be made on interface types. They cannot be made on concrete types, use 'reflect' package for that. 

```go
type MyInt int

func main() {
	var i interface{} = "hello"

	s := i.(string)
	fmt.Println(s)

	s, ok := i.(string)
	fmt.Println(s, ok)

	f, ok := i.(float64)
	fmt.Println(f, ok)
}
```

Type switch statements provide the ability to differentiate between multiple implementations of an interface that require different processing. They are most useful when there are only certain possible valid types that can be suppolied for an interface. 

Be sure to include default case in the type switch to handle not known implementations.

```go
func walkTree(t *treeNode) (int, error) {
    switch val := t.val.(type) {
        case nil:
            return 0, errors.New("invalid expression")
        case number:
            // we know that t.val is of type number, so return the 
            // int value
            return int(val), nil
        case operator:
            // we know that t.val is of type operator so find the values
            // of the left and right children, then call the process() 
            // method on operator to return the result
            left, err := walkTree(t.lchild)
            if err != nil {
                return 0, err
            }

            right, err := walkTree(t.rchild)
            if err != nil {
                return 0, err
            }

            return val.process(left, right), nil
        default:
            // if a new treeVal type is defined but walkTree wasn't 
            // updated to process it, this detects it
            return 0, errors.New("unknown node type")
    }
}
```

# function types are a bridge to interfaces

Methods can be added to function types. They allow functions to implements interfaces. The most common usage is for HTTP handlers.

```go
type Handler interface {
    ServeHTTP (http.ResponseWriter, *http.Request)
}

// By using a type conversion to http.HandlerFunc, any function that has
// the signature func(http.ResponseWriter, *http.Request) can be used
// as an http.Handler

type HandlerFunc func(http.ResponseWriter, *http.Request)

func (f HandlerFunc) ServeHttp(w http.ResponseWriter, r *http.Request) {
    f(w, r)
}
```

# dependency injection

```go
func LogOutput (message string) {
    fmt.Println(message)
}

// Data store
type SimpleDataStore struct {
    userData map[string]string
}

func (sds SimpleDataStore) UserNameForID(userID string) (string, bool) {
    name, ok := sds.userData[userID]
    return name, ok
}

// Factory function to create instance of SimpleDataStore
func NewSimpleDataStore() SimpleDataStore {
    return SimpleDataStore{
        userData: map[string]string {
            "1": "Fred",
            "2": "Mary",
            "3": "Pat",
        },
    }
}
```

Next, we'll write some business logic that looks up a user and says hello or goodbye. We need data so it requires a data store. We also want out logic to log when it is invoked so it depends on a logger. 

However, we don't want to force it to depend on `LogOutput` or `SimpleDataStore` because we might want to use a different logger or different data store later. Out business logic needs interfaces to describe what it depends on.

```go
type DataStore interface {
    UserNameForID(userID string) (string, bool)
}

type Logger interface {
    Log(message string)
}
```

To make our `LogOutput` function meet this interface, we define a function type with a method on it:

```go
type LoggerAdapter func(message string)

func (lg LoggerAdapter) Log(message string) {
    lg(message)
}
```

Our `LoggerAdapter` and `SimpleDataStore` meet the interfaces needed by our business logic, but neither type has any idea that it does. 

```go
type SimpleLogic struct {
    l   Logger
    ds  DataStore
}

func (sl SimpleLogic) SayHello(userID string) (string, error) {
    sl.l.Log("in SayHello for " + userID)

    name, ok := sl.ds.UserNameForID(userID)

    if !ok {
        return "", errors.New("unknown user")
    }

    return "Hello, " + name, nil
}

func (sl SimpleLogic) SayGoodbye(userID string) (string, error) {
    sl.l.Log("in SayGoodbye for " + userID)

    name, ok := sl.ds.UserNameForID(userID)

    if !ok {
        return "", errors.New("unknown user")
    }

    return "Goodbye, " + name, nil
}
```

There's nothing in our `SimpleLogic` that mentions the concrete types, so there's no dependency on them. We can later swap in new implementations from an entirely different provider because the provider has nothing to do with our interface. 

When we want a SimpleLogic instance, we call a factory function passing in interfaces and returning a struct

```go
func NewSimpleLogic(l Logger, ds DataStore) SimpleLogic {
    return SimpleLogic {
        l: l,
        ds: ds,
    }
}
```

Note the fields in SimpleLogic are unexported meaning they can only be accessed by code within the same package as SimpleLogic.

Our controller needs business logic that says hello, we define an interface for that

```go
type Logic interface {
    SayHello(userID string) (string, error)
}
```

This method is available in our `SimpleLogic` struct but the concrete type is not aware of the interface. Futher, the other method on `SimpleLogic`, `SayGoodbye`, is not in the interface because our controller doesn't care about it. 

```go
type Controller struct {
    l       Logger
    logic   Logic
}

func (c Controller) HandleGreeting(w http.ResponseWriter, r *http.Request) {
    c.l.Log("In SayHello")

    userID := r.URL.Query().Get("user_id")

    message, err := c.logic.SayHello(userID)

    if err != nil {
        w.WriteHeader(http.StatusBadRequest)
        w.Write([]byte(err.Error()))
        return
    }
    w.Write([]byte(message)])
}
```

Just as we have factory functions for our other types, let's write one for the controller

```go
func NewController(l Logger, logic Logic) Controller {
    return Controller {
        l:      l,
        logic:  logic,
    }
}
```

Finally, wire up all components in main func and start server

```go
func main() {
    l := LoggerAdapter(LogOutput)
    ds := NewSimpleDataStore()
    logic := NewSimpleLogic(l, ds)
    c := NewController(l, logic)

    http.HandleFunc("/hello", c.SayHello)

    http.ListenAndServe(":8080", nil)
}
```

The main function is the only part of the code that knows what all the concrete types actually are. 

# wire

If writing dependency injection by hand is too much work, use Wire, a dependency injection helper written by google.