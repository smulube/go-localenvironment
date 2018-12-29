# go-localenvironment

[![GoDoc](https://godoc.org/github.com/coreybutler/go-localenvironment?status.svg)](https://godoc.org/github.com/coreybutler/go-localenvironment)
[![Build Status](https://travis-ci.org/coreybutler/go-localenvironment.svg?branch=master)](https://travis-ci.org/coreybutler/go-localenvironment)

This is a port of the [coreybutler/localenvironment](https://github.com/coreybutler/localenvironment) Node.js module.

**tl;dr** Apply environment variables from a `env.json` in the working directory.

**Install** `go get github.com/coreybutler/go-localenvironment`

#### Overview

This is yet another environment variable management approach, based on a ridiculously simple concept.
This module looks for a file called `env.json` and loads each key as an environment variable,
accessible via the [os.Getenv](https://golang.org/pkg/os/#Getenv) method.

Consider the followwing directory structure:

```sh
> dir
  - main.exe (or whatever executable you generate with `go build`)
  - env.json
```

**env.json**

```json
{
  "MY_API_KEY": "12345"
}
```

**main.go**:

```go
package main

import (
  "os"
  "log"
  "github.com/coreybutler/go-localenvironment"
)

func main() {
  localenvironment.Apply() // Apply the env.json attributes to the environment variables.

  apiKey := os.Getenv("MY_API_KEY")

  log.Printf("My API key is %s.", apiKey)
}
```

Running `main.exe` (or equivalent binary) or `go run main.go` will output `My API key is 12345.`. The same Go app can be run in any
directory, each with a different `env.json` file, potentially yielding different results. It would also be possible to change a value in the `env.json` file, yielding a different result the next time the app is executed.

---

#### Why?

We use Linux, Windows, and OSX in my companies. We don't want to push sensitive configuration data
to git. Furthermore, different users may have different configuration values for the same variables
(think API keys, secrets, usernames, etc). As a result, we provide an `env.json` for each project
and developers fill in with the appropriate information. This allows us to synchronize our code
independently of the configuration.

There are also programs written that function differently _if an environment variable exists_. This
module won't fire an error if it doesn't find the `env.json`, so it's easy to manipulate the environment
simply by commenting out some code or changing a file name.

Your mileage will vary, but we've found this to be much simpler than writing shell/batch files and other
wrappers to manage environment variables.
