# API in Terminal

Instead of curl, httpie makes it easier.

## Installation

```bash
> brew install httpie
```

## Usage

```
http -f POST localhost:5000/api/auth/signup email=test@test.com username=hello password=test
```

This sends form data as a json object with email, username and password being key value pairs.