# Install

Use homebrew

# Setup

Find the path for $GOBIN and add that to the PATH var

```bash
$ go env
# find GOBIN and add that to the PATH var

# Add to .bashrc:
# export PATH=$PATH:/Users/brandon.kessler/go/bin

# after added to bashrc
$ source ~/.bashrc
```

# Packages

* golint
* 

Install golint

```bash
$ go install golang.org/x/lint/golint@latest
$ golint ./... # runs golint over entire project
```

# VS Code

* Install the Go extension and enable it
* Install/Update tools:

```
`cmd + shift + p` - to open the command pallete and type `go: install`
Click on `Go: Install/Update Tools` and select all 10 and install
```

# Makefile

Below is an example

* Each operation is called a target
* .DEFAULT_GOAL is what runs if we don't provide a command: `make`
  * We can also provide commands like `make lint`
  * In this case, it's the `build` target
* The word before the `:` is the name of the target
* Any words after the `:` are other targets that must run before the specified target runs
* The tasks performed by the target run on the indented lines
* .PHONY keeps `make` from getting confused if you ever create a directory 


```
.DEFAULT_GOAL := build

fmt:
	go fmt ./...
.PHONY:fmt

lint: fmt
	golint ./...
.PHONY:lint

vet: fmt lint
	go vet ./...
.PHONY:vet

build: vet
	go build hello.go
.PHONY:build
```

# multiple versions of go

```bash
$ go get golang.org/dl/go.1.15.6
$ go1.15.6 download 
$ go1.15.6 build # run go with the new version
```

Once ensured that the new version is breaking anything, then update the environment to use the new version.

