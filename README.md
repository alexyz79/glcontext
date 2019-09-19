# glcontext

![glcontext](https://github.com/moderngl/glcontext/raw/master/.github/icon.png)

**glcontext** is a library providing OpenGL implementation for ModernGL on multiple platforms.

## Backends

A glcontext backend is either an extension or a submodule of the glcontext package.
The package itself does not import any of the backends.
Importing the base package `glcontext` must safe and lightweight.

## Structure

Every backend of glcontext must provide a factory function:

```py
def create_context(*args, **kwargs) -> GLContext:
    pass
```

The create\_context method can take any number of positional and keyword arguments.
The factory function must return an object supporting the following methods:

```py
def load(self, name:str) -> int:
    pass
```

The load method takes an OpenGL function name as an input and returns a C/C++ function pointer as a python integer.
The return value must be 0 for not implemented functions.

```py
def __enter__(self, name:str):
    pass
```

The enter method calls `___MakeCurrent` to make the GLContext the calling thread's current rendering context.
`___MakeCurrent` stands for `wglMakeCurrent`, `glxMakeCurrent`, ...

```py
def __exit__(self, exc_type, exc_val, exc_tb):
    pass
```

The exit method calls `___MakeCurrent` to make the GLContext no longer current.

```py
def release(self):
    pass
```

The release method destroys the OpenGL context.

## Development Guide

There are "empty" example backends provided for developers to help adding new backends to the library.
There is a pure python example in [empty.py](#) and an extension example in [empty.cpp](#).
Besides their name match, they do not depend on each other, they are independent submodules of glcontext.

An "portable" backend implementation must load its dependency at runtime.
This rule is for simplifying the build of the entire package.
If an implementation cannot provide a "portable" backend, it will not be added to this library.
Non "portable" backends are welcome as third party libraries.

A backend must be lightweight, its size must fit within reasonable limits.

To add support for new platforms one must edit the `setup.py` too.
Platform specific dependencies are exceptions from the "portability" rule.

Example for platform specific dependencies:

- `gdi32.lib` on windows
- `libdl.a` on linux

Please note that `libGL.so` is loaded dinamically by the backends.

## Contributing

Contribution is welcome.

Pull Requests will be merged if they match the [Development Guide](#).

For prototypes, pure python implementations using ctypes are also welcome.
We will probably port it to a proper extension in the future.

Please ask questions [here](https://github.com/moderngl/glcontext/issues).