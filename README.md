Glow
======

Glow is an OpenGL binding generator for Go. Glow parses the [OpenGL XML API registry](https://cvs.khronos.org/svn/repos/ogl/trunk/doc/registry/public/api/) to produce a machine-generated cgo bridge between Go functions and native OpenGL functions. Glow is a fork of [GoGL2](https://github.com/chsc/gogl2).

Features:
- Bindings that mirror the C specification.
- Support for all OpenGL versions.
- Support for extensions.

Caveats:
- No support (yet) for debug callbacks.
- Very limiteded support for non-OpenGL packages (i.e., they may not compile without manual fixes).

Usage
-----

Until the API is stable Glow requires you to generate your own bindings.

    go get github.com/errcw/glow
    cd $GOPATH/src/github.com/errcw/glow
    go build
    ./glow download
    ./glow generate -g=all
    go install ./gl/<desired.version>/gl
    
Once the bindings are installed you can use them with the appropriate import statements.

```Go
import (
  "github.com/errcw/glow/gl/4.4/gl"
  "github.com/errcw/glow/glt"
)
```

A few notes about the packages:
- `gl`: Contains the OpenGL functions and enumeration values for the imported version.
- `glt`: Contains helper functions for OpenGL type conversions. Of note is `glt.Ptr` which takes a Go array or slice or pointer and returns a corresponding `uintptr` to use with functions expecting data pointers. Also of note is `glt.Str` which takes a null-terminated Go string and returns a corresponding `*int8` to use with functions expecting character pointers.

Once you have imported the necessary packages you must initialize the bindings.

```Go
func main() {
  if err := gl.Init(); err != nil {
    panic(err)
  }
}
```

A note about threading and goroutines. The bindings do not expose a mechanism to make an OpenGL context current on a different thread so you must restrict your usage to the thread on which you called `gl.Init()`. To do so you should use [LockOSThread](https://code.google.com/p/go-wiki/wiki/LockOSThread).


Examples
--------

A simple example illustrating how to use the bindings is available the [examples](https://github.com/errcw/glow/tree/master/examples) directory.

Function Loading
----------------

The `procaddr` package contains platform-specific functions for [loading OpenGL functions](https://www.opengl.org/wiki/Load_OpenGL_Functions). Calling `gl.Init()` uses the `auto` subpackage to automatically select an appropriate implementation based on the build environment. If you want to select a specific implementation you can use the `noauto` build tag and the `gl.InitWithProcAddrFunc` initialization function.
