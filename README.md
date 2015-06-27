# gohttplib

Shared library that exposes Go's `net/http.Server` with externally-bindable
handlers.

This is a silly project for experimenting with Go v1.5 buildmodes.

**Status**: Tiny subset of the `http.HandlerFunc` callback gets passed to a C
handler callback. Python bindings are working, too.


## Getting Started

Requirements:

- Go 1.5 or newer.
- C example: make, gcc
- Python example: cffi, python-cffi

```
$ git clone https://github.com/shazow/gohttplib/
$ cd gohttplib
$ make examples
```

### Example: C

C example can be linked against a shared object (`libgohttp.so` generated by
`go build -buildmode=c-shared`) or against a static library archive
(`libgohttp.a` generated by `go build -buildmode=c-archive`). By default, we
link against the shared object because that's what the Python example uses too.

Linked against our shared object:

```
$ make example-c
$ DYLD_LIBRARY_PATH=build/ ./build/gohttp-c
```

Note that you'll need to make sure that `libgohttp.so` is findable at runtime.

Now you can request `http://localhost:8000/hello` and the C handler in
`examples/c/main.c` will handle it!

Linked against our static library archive:

```
$ make example-c-static
$ ./build/gohttp-c-static
```

The static archive gets built into the binary so it's more portable during
runtime. Note the size differences:

```
8.8K  gohttp-c
5.1M  gohttp-c-static
7.5M  libgohttp.a
6.9M  libgohttp.so
```

### Example: Python

The Python example uses [python-cffi](https://cffi.readthedocs.org/en/latest/)
(you'll need python-cffi installed) to link against the shared object.

```
$ make example-python
$ cd examples/python
$ python -m gohttp
 * Running on http://127.0.0.1:5000/
```

Or write your own handler using this library:

```python
from gohttp import route, run

@route('/')
def index(w, req):
    w.write("%s %s %s\n" % (req.method, req.host, req.url))
    w.write("Hello, world.\n")

run(host='127.0.0.1', port=5000)
```

**Outstanding issues**:

- [#7](https://github.com/shazow/gohttplib/issues/7): [gohttp @ pypi](https://pypi.python.org/pypi/gohttp) ships with only with a shared object build for OSX. Won't work on other platforms.


## References & Credit

* [Go Execution Modes](https://docs.google.com/document/d/1nr-TQHw_er6GOQRsF6T43GGhFDelrAP0NqSS_00RgZQ/view#) document.
* Every single StackOverflow thread tagged [cgo].
* [github.com/jrick/buildmodes](https://github.com/jrick/buildmodes)
* [github.com/wolever/python-cffi-example](https://github.com/wolever/python-cffi-example/)
* Invaluable help with python-cffi from [@wolever](https://twitter.com/wolever),
  [@paultag](https://twitter.com/paultag), and
  [@dstufft](https://twitter.com/dstufft).


## Sponsors

This project was made possible thanks to [Glider Labs](http://gliderlabs.com/).


## License

MIT
