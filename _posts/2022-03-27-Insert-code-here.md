---
title: Insert code here
author: Fred
tags: template insert go release
---

Version 0.0.5 of Dredge extends the **templating functionality**. It allows inserting templated text into an existing file. The templated text can be inserted at the beginning or end of the file or inside of a Go function or Go import.

You read that right, Dredge (somewhat) understands Go source code and is able to place templated text at the end or beginning of a Go function. For imports, entries are deduplicated.

## HTTP server example

The example below adds an http service to a Go program by performing 3 steps:

 1. Edits the imports
 2. Adds setting up the http server in the main function
 3. Adds the handler function.

```yaml
workflows:
  - name: add-http-server
    description: Add a http server to the code
    steps:
      - template:
          input: |
            "fmt"
            "net/http"
          dest: main.go
          insert:
            section: import
      - template:
          input: |
            http.HandleFunc("/", root)
            http.ListenAndServe(":8080", nil)
          dest: main.go
          insert:
            section: func main()
            placement: end
      - template:
          input: |
            func root(w http.ResponseWriter, req *http.Request) {
                 fmt.Fprintf(w, "hello\n")
            }
          dest: main.go
          insert:
            placement: end
```

When applied to the following `main.go`:

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Printf("Hello World!\n")
}
```

This updates `main.go` to:

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	fmt.Printf("Hello World!\n")
	http.HandleFunc("/", root)
	http.ListenAndServe(":8080", nil)
}
func root(w http.ResponseWriter, req *http.Request) {
	fmt.Fprintf(w, "hello\n")
}
```

This will not work in all conditions, for example if root is already defined. But it provides a good starting point.

## Try it in 2 minutes

The dredge-repo contains a Dredgefile for Go that makes it easy to setup a new Go project and add a HTTP server to it. You can try it in a few minutes, without writing any Dredgefile. Initialise the project:

```
$ drg init go
Name of the executable [EXECUTABLE]: tool
✔ 1.18
go: creating new go.mod: module tool
```

Add the HTTP server:

```
$ drg add http-server
Port for the http server [HTTP_PORT]: 8080
main.go
```

After that you can run the service:

```
$ drg build
$ drg run
Hello World!
```

In a new terminal, check that the endpoint is available:

```
$ curl http://localhost:8080/
hello
```

**In a few minutes by running 2 commands you have a Go project that serves a HTTP endpoint and is easy to build & run.**
