<p align="center">
  <img src="https://raw.githubusercontent.com/d5/tengolang-share/master/logo_400.png" width="200" height="200">
</p>

# The Tengo Language

[![GoDoc](https://godoc.org/github.com/d5/tengo?status.svg)](https://godoc.org/github.com/d5/tengo)
[![Go Report Card](https://goreportcard.com/badge/github.com/d5/tengo)](https://goreportcard.com/report/github.com/d5/tengo)
[![CircleCI](https://circleci.com/gh/d5/tengo.svg?style=svg)](https://circleci.com/gh/d5/tengo)

**Tengo is a small, dynamic, fast, secure script language for Go.** 

Tengo is **[fast](#benchmark)** and secure because it's compiled/executed as
bytecode on stack-based VM that's written in native Go.

```golang
/* The Tengo Language */
fmt := import("fmt")

each := func(seq, fn) {
    for x in seq { fn(x) }
}

sum := func(init, seq) {
    each(seq, func(x) { init += x })
    return init
}

fmt.println(sum(0, [1, 2, 3]))   // "6"
fmt.println(sum("", [1, 2, 3]))  // "123"
```

> Test this Tengo code in the
> [Tengo Playground](https://tengolang.com/?s=0c8d5d0d88f2795a7093d7f35ae12c3afa17bea3)

## Features

- Simple and highly readable
  [Syntax](https://github.com/d5/tengo/blob/master/docs/tutorial.md)
  - Dynamic typing with type coercion
  - Higher-order functions and closures
  - Immutable values
- [Securely Embeddable](https://github.com/d5/tengo/blob/master/docs/interoperability.md)
  and [Extensible](https://github.com/d5/tengo/blob/master/docs/objects.md)
- Compiler/runtime written in native Go _(no external deps or cgo)_
- Executable as a
  [standalone](https://github.com/d5/tengo/blob/master/docs/tengo-cli.md)
  language / REPL
- Use cases: rules engine, [state machine](https://github.com/d5/go-fsm),
  data pipeline, [transpiler](https://github.com/d5/tengo2lua)

## Benchmark

| | fib(35) | fibt(35) |  Type  |
| :--- |    ---: |     ---: |  :---: |
| Go | `48ms` | `3ms` | Go (native) |
| [**Tengo**](https://github.com/d5/tengo) | `2,349ms` | `5ms` | VM on Go |
| Lua | `1,416ms` | `3ms` | Lua (native) |
| [go-lua](https://github.com/Shopify/go-lua) | `4,402ms` | `5ms` | Lua VM on Go |
| [GopherLua](https://github.com/yuin/gopher-lua) | `4,023ms` | `5ms` | Lua VM on Go |
| Python | `2,588ms` | `26ms` | Python (native) |
| [starlark-go](https://github.com/google/starlark-go) | `11,126ms` | `6ms` | Python-like Interpreter on Go |
| [gpython](https://github.com/go-python/gpython) | `15,035ms` | `4ms` | Python Interpreter on Go |
| [goja](https://github.com/dop251/goja) | `5,089ms` | `5ms` | JS VM on Go |
| [otto](https://github.com/robertkrimen/otto) | `68,377ms` | `11ms` | JS Interpreter on Go |
| [Anko](https://github.com/mattn/anko) | `92,579ms` | `18ms` | Interpreter on Go |

_* [fib(35)](https://github.com/d5/tengobench/blob/master/code/fib.tengo):
Fibonacci(35)_  
_* [fibt(35)](https://github.com/d5/tengobench/blob/master/code/fibtc.tengo):
[tail-call](https://en.wikipedia.org/wiki/Tail_call) version of Fibonacci(35)_  
_* **Go** does not read the source code from file, while all other cases do_  
_* See [here](https://github.com/d5/tengobench) for commands/codes used_

## Quick Start

```
go get github.com/d5/tengo/v2
```

A simple Go example code that compiles/runs Tengo script code with some input/output values:

```golang
package main

import (
	"context"
	"fmt"

	"github.com/d5/tengo/v2"
)

func main() {
	// Tengo script code
	src := `
each := func(seq, fn) {
    for x in seq { fn(x) }
}

sum := 0
mul := 1
each([a, b, c, d], func(x) {
	sum += x
	mul *= x
})`

	// create a new Script instance
	script := tengo.NewScript([]byte(src))

	// set values
	_ = script.Add("a", 1)
	_ = script.Add("b", 9)
	_ = script.Add("c", 8)
	_ = script.Add("d", 4)

	// run the script
	compiled, err := script.RunContext(context.Background())
	if err != nil {
		panic(err)
	}

	// retrieve values
	sum := compiled.Get("sum")
	mul := compiled.Get("mul")
	fmt.Println(sum, mul) // "22 288"
}
```

## References

- [Language Syntax](https://github.com/d5/tengo/blob/master/docs/tutorial.md)
- [Object Types](https://github.com/d5/tengo/blob/master/docs/objects.md)
- [Runtime Types](https://github.com/d5/tengo/blob/master/docs/runtime-types.md)
  and [Operators](https://github.com/d5/tengo/blob/master/docs/operators.md)
- [Builtin Functions](https://github.com/d5/tengo/blob/master/docs/builtins.md)
- [Interoperability](https://github.com/d5/tengo/blob/master/docs/interoperability.md)
- [Tengo CLI](https://github.com/d5/tengo/blob/master/docs/tengo-cli.md)
- [Standard Library](https://github.com/d5/tengo/blob/master/docs/stdlib.md)