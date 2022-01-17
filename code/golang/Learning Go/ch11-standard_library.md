# io

Two interfaces defined in this package: `io.Reader` and `io.Writer`

`io.WriteFile` functions are fine for small data sources but it's better to use the `Reader`, `Writer`, and `Scanner` in the `bufio` package to work with larger data sources. 

There is a pattern for adding a method to a Go type. Example, if you have a type that implements io.Reader but not io.Closer and need to pass it to a function that expects an io.ReadCloser, pass your io.Reader into ioutil.NopCloser and get back a type that implements io.ReadCloser.

```go
type nopCloser struct {
    io.Reader
}

func (nopCloser) Close() error { return nil }

func NopCloser(r io.Reader) io.ReadCloser {
    return nopCloser{r}
}
```

Any time you need to add additional methods to a type so it can meet an interface, use this embedded type pattern.

# time

Two main types used to represent time, `time.Duration` and `time.Time`.

A length of time is represented with `time.Duration`

```go
// Represent a duration of two hours and thirty minutes with:
d := 2 * time.Hour + 30 * time.Minute // d is of type time.Duration
```

Parse string formats into time.Duration formats using time.ParseDuration.

An instant of time is represented with `time.Time`.

When comparing times, you should not use `==`, instead use the `Equal` method which corrects for time zone.

`time.Parse` converts from a string to a `time.Time` while the `Format` method converts a `time.Time` to a `string`.

```go
t, err := time.Parse("2006-02-01 15:04:05 -0700", "2016-13-03 00:00:00 +0000")

if err != nil {
    return err
}

ftm.Println(t.Format("January 2, 2006 at 3:04:05PM MST"))
```

# json

`encoding/json` package

Let's say we have to read and write the following JSON:

```json
{
    "id": "12345",
    "date_ordered": "2020-05-01T13:01:02Z",
    "customer_id": "3",
    "items":[
        {"id":"xyz123", "name": "Thing 1"},
        {"id":"abc789", "name": "Thing 2"}
    ]
}
```

We define types to map this data

```go
type Order struct {
    ID          string      `json:"id"`
    DateOrdered time.Time   `json:"date_ordered"`
    CustomerID  string      `json:"customer_id"`
    Items       []Item      `json:"items"`
}

type Item struct {
    ID  string  `json:"id"`
    Name string `json:"name"`
}
```

We use *struct tags* to specify processing rules. If no json tag is provided, the default behavior is to assume that the name of the JSON object field matches the name of the Go struct field. It's best practice to use the struct tag to specify the name for the field explicitly, even if it is identical to the field name.

The `Unmarshal` function in the `encoding/json` package is used to convert a slice of bytes into a struct. 

If we have a string named `data`, this converts it to a struct of type `Order`

```go
var o Order

err := json.Unmarshal([]byte(data), &o)

if err != nil {
    return err
}
```

We could write to an in-memory byte slice buffer using json.Marshal and then write that byte slice to the network or disk, but it would be better if we could write to an io.Writer directly. We can do this with `json.Decoder` and `json.Encoder`

Starting with data in toFile

```go
type Person struct {
    Name    string  `json:"name"`
    Age     int     `json:"age"`
}

toFile := Person {
    Name:   "Fred",
    Age:    40,
}
```

The `os.File` type implements both the `io.Reader` and the `io.Writer` interfaces, we can use it to demonstrate `json.Decoder` and `json.Encoder`. First, write toFile to a temp file by passing the temp file to json.NewEncoder, which returns a json.Encoder for the temp file. Then pass toFile to the Encode method.

```go
tmpFile, err := ioutil.TempFile(os.TempDir(), "sample-")

if err != nil {
    panic(err)
}

defer os.Remove(tmpFile.Name())

err = json.NewEncoder(tmpFile).Encode(toFile)

if err != nil {
    panic(err)
}

err = tmpFile.Close()

if err != nil {
    panic(err)
}
```

Once toFile is written, we can read the JSON back by passing a reference to the temp file to json.NewDecoder and then calling the Decode method with a variable type of Person:

```go
tmpFile2, err := os.Open(tmpFile.Name())

if err != nil {
    panic(err)
}

var fromFile Person

err = json.NewDecoder(tmpFile2).Decode(&fromFile)

if err != nil {
    panic(err)
}

err = tmpFile2.Close()

if err != nil {
    panic(err)
}

fmt.Printf("%+v\n", fromFile)
```

## Encoding and Decoding JSON Streams

When you have multiple JSON structs to read or write at once, use `json.Decoder` and `json.Encoder` again. Assume the following data:

```json
{"name": "Fred", "age": 40}
{"name": "Mary", "age": 21}
{"name": "Pat", "age": 30}
```

Assume it's stored ina string called data but this could be from a file or from a get request. Store this data into our `t` variable, one JSON object a time. Like before, initialize our `json.Decoder` with the data source but use the `More` method on `json.Decoder` as a for loop condition.

```go
dec := json.NewDecoder(strings.NewReader(data))

for dec.More() {
    err := dec.Decode(&t)

    if err != nil {
        panic(err)
    }

    // process t
}
```

To limit amount of code that cares about what your JSON looks like, define two different structs. Use one for converting to and from JSON and the other for data processing. Read in JSON to your JSON-aware type, and then copy it to the other. When you write out JSON, do the reverse. This does create some duplication, but it keeps your business logic from depending on wire protocols. 

# net/http

## client

The `net/http` package defines a `Client` type to make HTTP requests and receive HTTP responses. A default client instance (`DefaultClient`) is found in the package but avoid using it in production. Instead, instantiate your own and provide a timeout. You only need to create a single `http.Client` for your entire program as it handles multiple simultaneous requests across goroutines.

```go
client := &http.Client{
    Timeout: 30 * time.Second,
}
```

To make a request, create a new `*http.Request` instance with the `http.NewRequestWithContext` func. If making a PUT, POST, or PATCH req, specify the body with the last parameter as an io.Reader. If there is no body, use `nil`.

```go
req, err := http.NewRequestWithContext(context.Background(), 
                                       http.MethodGet,
                                       "https://jsonplaceholder.typicode.come/todos/1",
                                       nil)
if err != nil {
    panic(err)
}
```

Once you have an *http.Request instance, you can set any headers via the Headers field of the instance. 

```go
req.Header.Add("X-My-Client", "Learning Go")

res, err := client.Do(req)

if err != nil {
    panic(err)
}
```

The response has several fields with info on the req:

* StatusCode
* Status
* Header
* Body - type `io.ReadCloser`

This allows us to use it with the `json.Decoder` to process API responses

```go
defer res.Body.Close()

if res.StatusCode != http.StatusOK {
    panic(fmt.Sprintf("unexpected status: got %v", res.Status))
}

fmt.Println(res.Header.Get("Content-Type"))

var data struct {
    UserID      int     `json:"userId"`
    ID          int     `json:"id"`
    Title       string  `json:"title"`
    Completed   bool    `json:"completed"`
}

err = json.NewDecoder(res.Body).Decode(&data)

if err != nil {
    panic(err)
}

fmt.Printf("%+v\n", data)
```