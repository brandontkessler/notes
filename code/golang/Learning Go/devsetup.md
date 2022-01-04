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

Install golint

```bash
$ go install golang.org/x/lint/golint@latest
$ golint ./... # runs golint over entire project
```


