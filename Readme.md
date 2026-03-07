# Go Natural Date

[![GoDoc](https://godoc.org/github.com/tj/go-naturaldate?status.svg)](https://godoc.org/github.com/tj/go-naturaldate)
![](https://img.shields.io/badge/license-MIT-blue.svg)
![](https://img.shields.io/badge/status-stable-green.svg)

Natural date time parsing for Go. This project is a fork of [tj/go-naturaldate](https://github.com/tj/go-naturaldate). This package was designed for parsing human-friendly relative date/time ranges (e.g., "today", "5 minutes ago", "next month") in [Apex Logs](https://apex.sh/logs/)' command-line log search, but is flexible enough to be used in any Go project that requires natural language date parsing.

## Installation

```sh
go get github.com/tj/go-naturaldate
```

## Usage

Use `naturaldate.Parse` to parse a natural language date string into a `time.Time`. It requires a reference time (`base` or `now`) to resolve relative expressions.

```go
package main

import (
	"fmt"
	"log"
	"time"

	"github.com/tj/go-naturaldate"
)

func main() {
	// Parse a basic relative time
	t, err := naturaldate.Parse("5 minutes ago", time.Now())
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(t)

	// You can also parse specific dates or times
	t, err = naturaldate.Parse("December 25th at 7:30am", time.Now())
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(t)
}
```

### Parsing Durations

You can also parse a relative duration using `naturaldate.ParseDuration`.

```go
d, err := naturaldate.ParseDuration("1 year and 2 months", time.Now())
if err == nil {
	fmt.Println(d) // e.g., 10248h0m0s
}

p, err := naturaldate.ParseDuration("1 year and 2 months", time.Now(), naturaldate.WithDirection(naturaldate.Past))
if err == nil {
	fmt.Println(p) // e.g., -10224h0m0s
}
```

## Examples

Here are some examples of the types of expressions currently supported. Arbitrary text is generally ignored (e.g., "Remind me in...").

- `now`
- `today`
- `yesterday`
- `5 minutes ago`
- `three days ago`
- `last month`
- `next month`
- `one year from now`
- `yesterday at 10am`
- `last sunday at 5:30pm`
- `sunday at 22:45`
- `next January`
- `last February`
- `December 25th at 7:30am`
- `10am`
- `10:05pm`
- `10:05:22pm`
- `Restart the server in 5 days from now`
- `Remind me on the 25th of December at 7:30am`
- `Message me in two weeks`

See the [tests](./naturaldate_test.go) for more examples.

## Direction

A default direction can be applied using `naturaldate.WithDirection()` for ambiguous expressions such as `sunday`, or `september`. By default, `naturaldate.Past` is used, so they will be equivalent to `last sunday` and `last september`.

```go
// Treat "sunday" as "next sunday"
t, err := naturaldate.Parse("sunday", time.Now(), naturaldate.WithDirection(naturaldate.Future))
```

## Contributing

To build the parser first install [pointlander/peg](https://github.com/pointlander/peg), then run `go generate`:

```sh
go get github.com/pointlander/peg
go generate ./...
```