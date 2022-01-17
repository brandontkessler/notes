While you can use a relative path to import a dependent package within the same module, don't. Absolute import paths clarify what you are importing and make it easier to refactor your code.

The name of a package is determined by its package clause, not its import path.

As a general rule, make the name of the package match the name of the directory that contains the package.

# naming packages

Package name should be descriptive. 

For example, say there are two helper functions: one to extract all names from a string and another to format names properly. Don't create two functions in a `util` package, one called `ExtractNames` and the other called `FormatNames`.

When these are called it would be `util.ExtractNames` and `util.FormatNames`.

It's better to create one function called `Names` in a package called `extract` and second function called `Names` in a package called `format`. It's ok for these function to have the same name because they are in different packages. The first referred to as `extract.Names` when imported and the second as `format.Names`. 

Avoid repeating the package name in function name, ex. `extract.ExtractNames`

# how to organize your module

When module is small, keep it all in a single package. 

If module consists of one or more applications, create a directory called `cmd` at the root of module. Within `cmd` create one directory for each binary built from your module. Use `main` as the package name within each of these directories. 

External packages go in a directory called pkg. 

Internal packages go in a directory called internal. 

# overriding package names

```go
import (
    crand "crypto/rand"
    "encoding/binary"
    "fmt"
    "math/rand"
)
```

The above renames `crypto/rand` as crand to avoid importing two packages with the same name "rand" one from math and one from crypto.

# package comments and godoc

Convention for godoc

1. Comment is written directly before the item being documented with no blank lines between the comment and the declaration of the item
2. The comment starts with the name of the item.
3. Use a blank comment to break your comment into multiple paragraphs
4. Insert preformatted comments by indenting the lines

If the package has a lot of length comments (extensive formatting), put the comments in a file in your package called doc.go.

```go
// Package money provides various utilities to make it easy to manage money.
package money

// Money represents the combination of an amount of money
// and the currency the money is in.
type Money struct {
    Value decimal.Decimal
    Currency string
}

// Convert converts the value of one currency to another.
//
// It has two parameters: a Money instance with the value to convert,
// and a string that represents the currency to convert to. Convert returns a
// the converted currency and any errors encountered from unknown or unconvertable
// currencies.
// If an error is returned, the Money instance is set to the zero value.
//
// Supported currencies are:
//      USD - US Dollar
//      CAD - Canadian Dollar
//      EUR - Euro
//      INR - Indian Rupee
//
// More information on exchange rates can be found at https://www.etc.com
func Convert(from Money, to string) (Money, error) {
    // ...
}
```

To see godoc for a package, in command line use:

```bash
$ # go install -v golang.org/x/tools/cmd/godoc@latest
$ go doc PACKAGE_NAME.IDENTIFIER_NAME
```

# internal package

When you create a package called `internal`, the exported identifiers in the package and its sub-packages are only accessible to the direct parent package of `internal` and the siblings.

# using aliases

```go
type Foo struct {
    x int
    s string
}

func (f Foo) Hello() string {
    return "hello"
}

func (f Foo) goodbye() string {
    return "goodbye"
}

// using an alias to allow users to access Foo by the name Bar
type Bar = Foo

func MakeBar() Bar {
    bar := Bar {
        a: 20,
        B: "Hello",
    }

    var f Foo = bar
    fmt.Println(f.Hello())
    return bar
}
```

# versioning

Go picks the latest version of a dependency when you add it to your project.

You can specify an earlier version of the module. To see what versions of the module are available, use `go list`

```bash
$ go list -m -versions github.com/learning-go-book/simpletax
```

To downgrade, use `go get` command

```bash
$ go get github.com/learning-go-book/simpletax@v1.0.0
```

Software uses *semantic versioning* aka SemVer. There are three parts separated by a dot: major.minor.patch and is preceded by a v as in v2.3.1 as an example. 

* patch is incremented when fixing a bug
* minor is incremented (and patch set back to 0) when a new, backwards-compatible feature is added
* major version is incremented when making a change that breaks backwards-compatibility

To upgrade the bug patch release for a current minor version, use:

```bash
$ go get -u=patch github.com/learning-go-book/simpletax
```
