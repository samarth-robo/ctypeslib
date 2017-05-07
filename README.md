# ctypeslib with libclang

[![Build Status](https://travis-ci.org/trolldbois/ctypeslib.svg?branch=master)](https://travis-ci.org/trolldbois/ctypeslib)
[![Coverage Status](https://coveralls.io/repos/trolldbois/ctypeslib/badge.svg)](https://coveralls.io/r/trolldbois/ctypeslib)
[![Code Health](https://landscape.io/github/trolldbois/ctypeslib/master/landscape.svg?style=flat)](https://landscape.io/github/trolldbois/ctypeslib/master)
[![pypi](https://img.shields.io/pypi/dm/ctypeslib.svg)](https://pypi.python.org/pypi/ctypeslib2)

[Quick usage guide](docs/ctypeslib 2.0 Introduction.ipynb) in the docs/ folder.

## Status update

2017-05-01: master branch works with libclang-4.0 HEAD

## Installation

On Ubuntu, libclang libraries are installed with versions.
This library tries to load a few different versions to help you out. (__init__.py)
But if you encounter a version compatibility issue, you might have to fix the problem
using one of the following solutions:

1. Install libclang1-dev to get libclang.so (maybe)
2. OR create a link to libclang-4.0.so.1 named libclang.so
3. OR hardcode a call to clang.cindex.Config.load_library_file('libclang-4.0.so.1') in your code


### Pypi

Stable Distribution is available through PyPi at https://pypi.python.org/pypi/ctypeslib2/

`sudo pip install ctypeslib2`

### Setting up clang >= 3.7 dependency

See the LLVM Clang instructions at http://llvm.org/apt/.

## Examples

Look at `test/test_example_script.py`

Other example:

Source file:
```
$ cat t.c 
struct my_bitfield
{
  long a:3;
  long b:4;
  unsigned long long c:3;
  unsigned long long d:3;
  long f:2;
} ;
```
Run c-to-python script:
`clang2py t.c`
Output:
```
# -*- coding: utf-8 -*-
#
# TARGET arch is: []
# WORD_SIZE is: 8
# POINTER_SIZE is: 8
# LONGDOUBLE_SIZE is: 16
#
import ctypes

class struct_my_bitfield(ctypes.Structure):
    _pack_ = True # source:False
    _fields_ = [
    ('a', ctypes.c_int64, 3),
    ('b', ctypes.c_int64, 4),
    ('c', ctypes.c_int64, 3),
    ('d', ctypes.c_int64, 3),
    ('f', ctypes.c_int64, 2),
    ('PADDING_0', ctypes.c_int64, 49),
     ]

__all__ = \
    ['struct_my_bitfield']
```




## Usage
```
usage: clang2py [-h] [-c] [-d] [--debug] [-e] [-k TYPEKIND] [-i] [-l DLL]
                [-m module] [-o OUTPUT] [-p DLL] [-q] [-r EXPRESSION]
                [-s SYMBOL] [-t TARGET] [-v] [-V] [-w W] [-x]
                [--show-ids SHOWIDS] [--max-depth N] [--clang-args CLANG_ARGS]
                files [files ...]

Version 2.1.5rc0. Generate python code from C headers

positional arguments:
  files                 source filenames. stdin is not supported

optional arguments:
  -h, --help            show this help message and exit
  -c, --comments        include source doxygen-style comments
  -d, --doc             include docstrings containing C prototype and source
                        file location
  --debug               setLevel to DEBUG
  -e, --show-definition-location
                        include source file location in comments
  -k TYPEKIND, --kind TYPEKIND
                        kind of type descriptions to include: a = Alias, c =
                        Class, d = Variable, e = Enumeration, f = Function, m
                        = Macro, #define s = Structure, t = Typedef, u = Union
                        default = 'cdefstu'
  -i, --includes        include declaration defined outside of the sourcefiles
  -l DLL, --include-library DLL
                        library to search for exported functions. Add multiple
                        times if required
  -m module, --module module
                        Python module(s) containing symbols which will be
                        imported instead of generated
  -o OUTPUT, --output OUTPUT
                        output filename (if not specified, standard output
                        will be used)
  -p DLL, --preload DLL
                        dll to be loaded before all others (to resolve
                        symbols)
  -q, --quiet           Shut down warnings and below
  -r EXPRESSION, --regex EXPRESSION
                        regular expression for symbols to include (if neither
                        symbols nor expressions are specified,everything will
                        be included)
  -s SYMBOL, --symbol SYMBOL
                        symbol to include (if neither symbols nor expressions
                        are specified,everything will be included)
  -t TARGET, --target TARGET
                        target architecture (default: x86_64-Linux)
  -v, --verbose         verbose output
  -V, --version         show program's version number and exit
  -w W                  add all standard windows dlls to the searched dlls
                        list
  -x, --exclude-includes
                        Parse object in sources files only. Ignore includes
  --show-ids SHOWIDS    Don't compute cursor IDs (very slow)
  --max-depth N         Limit cursor expansion to depth N
  --clang-args CLANG_ARGS
                        clang options, in quotes: --clang-args="-std=c99
                        -Wall"

Cross-architecture: You can pass target modifiers to clang. For example, try
--clang-args="-target x86_64" or "-target i386-linux" to change the target CPU
arch.
```

## Inner workings for memo

- clang2py is a script that calls ctypeslib/ctypeslib/clang2py.py
- clang2py.py is mostly the old xml2py.py module forked to use libclang.
- clang2py.py calls ctypeslib/ctypeslib/codegen/codegenerator.py
- codegenerator.py calls ctypeslib/ctypeslib/codegen/clangparser.py
- clangparser.py uses libclang's python binding to access the clang internal 
 representation of the C source code. 
 It then translate each child of the AST tree to python objects as listed in 
 typedesc.
- codegenerator.py then uses these python object to generate ctypes-based python
 source code.
 
Because clang is capable to handle different target architecture, this fork 
 {is/should be} able to produce cross-platform memory representation if needed.


## Credits

This fork of ctypeslib is mainly about using the libclang1>=3.7 python bindings
to generate python code from C source code, instead of gccxml.

the original ctypeslib contains these packages:
 - ``ctypeslib.codegen``       - a code generator
 - ``ctypeslib.contrib``       - various contributed modules
 - ``ctypeslib.util``          - assorted small helper functions
 - ``ctypeslib.test``          - unittests

This fork of ctypeslib is heavily patched for clang.
- https://github.com/trolldbois/ctypeslib is based on 
 rev77594 of the original ctypeslib.
- git-svn-id: http://svn.python.org/projects/ctypes/trunk/ctypeslib@77594 
 6015fed2-1504-0410-9fe1-9d1591cc4771

The original ctypeslib is written by
- author="Thomas Heller",
- author_email="theller@ctypes.org",
