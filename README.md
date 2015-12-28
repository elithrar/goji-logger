# goji/logger

**goji/logger** provides request logging and ID generation middleware for
[Goji](https://goji.io/), a HTTP multiplexer written in Go.

The middleware functions include:

* [Logger](https://godoc.org/github.com/goji/logger#Logger) - a request logger that logs the method, remote IP and execution time
  of the request to stdout. When outputting to a terminal, the output is
  colorized for readability.
* [RequestID](https://godoc.org/github.com/goji/logger#RequestID) generates a random base62 identifier for the request. When used
  before the `Logger` middleware, the RequestID is included in the log output.

Here's what it looks like:

<p align="center">
  <img src="https://github.com/goji/logger/raw/master/output_example.png" title="Terminal output"/>
</p>

## Install It

```sh
$ go get github.com/goji/logger
```

## Example

Here's an example of the `logger.RequestID` and `logger.Logger` middleware in
action:

```go
package main

import (
	"fmt"
	"log"
	"net/http"

	"golang.org/x/net/context"

	"github.com/goji/logger"
	"goji.io"
	"goji.io/pat"
)

func main() {
	mux := goji.NewMux()
    // Include logger.RequestID before logger.Logger, and the request ID will
    // automatically be added to the log output.
	mux.UseC(logger.RequestID)
	mux.UseC(logger.Logger)

	mux.HandleFuncC(pat.Get("/"), SomeHandler)

	log.Fatal(http.ListenAndServe("localhost:8000", mux))
}

func SomeHandler(ctx context.Context, w http.ResponseWriter, r *http.Request) {
	fmt.Fprint(w, "Goji!\n")
}
```

Note: Provided you implement the `goji.Handler` interface in your own
application, this middleware can be used beyond just Goji.

## License

MIT licensed. See the LICENSE file for details.
