# How to Cmake Good

base on [this youtube series](https://www.youtube.com/watch?v=_yFPO1ofyF0&list=PLK6MXr8gasrGmIiSuVQXpfFuE1uPT615s&index=1)

## targets

### executable

`add_executable(target_name srcs)`

### library

`add_library(lib_name srcs)`

To set internal include path for library source files to get to the headers: `target_include_directories(lib_name PRIVATE header_dir)`.

To expose the header path to users:  `target_include_directories(lib_name INTERFACE header_dir)`.

The same semantics of `PRIVATE/INTERFACE/PUBLIC` keywords applies to link a library inside a library.

## scripting

[An comprehensive document about cmake scripting](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html)

Below is only a small but useful subset.

### variable

assignment: `set(var_name str1 str2 str3)`.

dereference: `${var_name}`

> *note*: 
> 
> about list expansion: 
> if passed as a function argument,`"${var_name}"` will evaluate to `"str1;str2;str3"`, while `${var_name}"` will expand to three arguments.

### control flow

`if()`, `foreach()`, `while()` used with their corresponding `end*()` counterpart.

[if evaluation](https://cmake.org/cmake/help/latest/command/if.html#basic-expressions)

### math

`math(EXPR store_result_var_name expr)`

### function

`function(func_name arg1 arg2 arg3)` with `endfunction()`

[advanced argument parsing](https://www.youtube.com/watch?v=mBjRjZcRTA0&list=PLK6MXr8gasrGmIiSuVQXpfFuE1uPT615s&index=12)

### include

`include(cmake/<scriptname>.cmake)`

## default variables

### directory

these are always strings of the absolute path

`CMAKE_SOURCE_DIR`: the `CMakeLists.txt` specified when calling `cmake` progam.

`CMAKE_BINARY_DIR`: the directory where `cmake` is called.

`CMAKE_CURRENT_SOURCE_DIR` and `CMAKE_CURRENT_BINARY_DIR`: changed when `add_subdirectory()` is called and restored when it returns.

`CMAKE_CURRENT_LIST_FILE`: the current file executing, regardless of whether it is named `CMakeLists.txt`.

`CMAKE_CURRENT_LIST_DIR`: the directory of `CMAKE_CURRENT_LIST_FILE`.

## project structure

https://medium.com/swlh/c-project-structure-for-cmake-67d60135f6f5
