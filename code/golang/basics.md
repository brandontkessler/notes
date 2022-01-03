## Naming a module

`<prefix>/<descriptive-text>`

* prefix generally describes the origin of the module, ex. github.com/<project-name>
* project name is any name that you control (avoid common terms like widget or app)

## Module setup

```bash
$ mkdir hello && cd hello
$ go mod init example.com/hello
```

## Calling a module from another

```bash
$ cd ..
$ mkdir goodbye && cd goodbye
$ go mod init example.com/goodbye
$ go mod edit -replace example.com/hello=../hello
$ go mod tidy
```

The `go mod edit` command replaces the module name with the relative path to it
The `go mod tidy` command updates the mod file
