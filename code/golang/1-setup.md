# Setting Bin

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

## Installing and Running the Module

Inside of a module named 'hello':

```bash
$ go build
$ go install
$ hello # runs the module
```