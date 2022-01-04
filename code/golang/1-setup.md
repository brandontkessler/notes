# Setting Bin

Find the path for $GOBIN and add that to the PATH var

```bash
$ go env
# find GOBIN and add that to the PATH var

$ # export PATH=$PATH:/Users/brandon.kessler/go/bin

# Or add to .bashrc:
# export PATH=$PATH:/Users/brandon.kessler/go/bin

# after added to bashrc
$ source ~/.bashrc
```

If gopath needed to be changed to '/bin' for example:

```bash
# If path $HOME/bin doesn't already exist
$ mkdir $HOME/bin
$ ls $HOME

# Add $HOME/bin to PATH
$ export PATH=$PATH:$HOME/bin

# Or add to .bashrc:
# PATH=$PATH:$HOME/bin
# export PATH

# Set the installation location for Go
$ go env -w GOBIN=/Users/brandon.kessler/bin

# Print the environment variables with:
$ go env
```

# Naming a module

`<prefix>/<descriptive-text>`

* prefix generally describes the origin of the module, ex. github.com/<project-name>
* project name is any name that you control (avoid common terms like widget or app)

# Module setup

```bash
$ mkdir hello && cd hello
$ go mod init example.com/hello
```

# Calling a module from another

```bash
$ cd ..
$ mkdir goodbye && cd goodbye
$ go mod init example.com/goodbye
$ go mod edit -replace example.com/hello=../hello
$ go mod tidy
```

The `go mod edit` command replaces the module name with the relative path to it
The `go mod tidy` command updates the mod file


# Installing and Running the Module

Inside of a module named 'hello':

```bash
$ go build
$ go install
$ hello # runs the module
```
