# API Reference Documentation

## Overview
This document provides a comprehensive reference for all API endpoints and functions available in the Termina-js JavaScript Interpreter.

## Core API Functions

### Interpreter State Management

#### `js_State *js_newstate(js_Alloc alloc, void *uctx, int flags)`
Creates a new interpreter state for executing JavaScript code.

**Parameters:**
- `alloc`: Memory allocator function pointer
- `uctx`: User context data
- `flags`: Configuration flags

**Returns:** Pointer to new `js_State` structure

**Example:**
```c
js_State *J = js_newstate(NULL, NULL, 0);
```

#### `void js_freestate(js_State *J)`
Releases all resources associated with an interpreter state.

**Parameters:**
- `J`: Pointer to the interpreter state to free

**Example:**
```c
js_freestate(J);
```

### Code Execution

#### `int js_dostring(js_State *J, const char *code)`
Executes JavaScript code from a string.

**Parameters:**
- `J`: Interpreter state
- `code`: JavaScript source code as string

**Returns:** 0 on success, non-zero on error

**Example:**
```c
js_dostring(J, "var x = 42; print(x);");
```

#### `int js_dofile(js_State *J, const char *filename)`
Executes JavaScript code from a file.

**Parameters:**
- `J`: Interpreter state
- `filename`: Path to JavaScript file

**Returns:** 0 on success, non-zero on error

**Example:**
```c
js_dofile(J, "script.js");
```

### Stack Operations

#### `void js_pushnumber(js_State *J, double value)`
Pushes a numeric value onto the stack.

**Parameters:**
- `J`: Interpreter state
- `value`: Double precision floating point number

**Example:**
```c
js_pushnumber(J, 3.14159);
```

#### `void js_pushstring(js_State *J, const char *str)`
Pushes a string value onto the stack.

**Parameters:**
- `J`: Interpreter state
- `str`: Null-terminated C string

**Example:**
```c
js_pushstring(J, "Hello, JavaScript!");
```

#### `void js_pushboolean(js_State *J, int value)`
Pushes a boolean value onto the stack.

**Parameters:**
- `J`: Interpreter state
- `value`: Non-zero for true, zero for false

**Example:**
```c
js_pushboolean(J, 1);  // Push true
```

#### `void js_pushundefined(js_State *J)`
Pushes undefined onto the stack.

**Parameters:**
- `J`: Interpreter state

**Example:**
```c
js_pushundefined(J);
```

### Type Conversion

#### `double js_tonumber(js_State *J, int index)`
Converts a stack value to a number.

**Parameters:**
- `J`: Interpreter state
- `index`: Stack index (1-based from function arguments)

**Returns:** Converted numeric value

**Example:**
```c
double num = js_tonumber(J, 1);
```

#### `const char *js_tostring(js_State *J, int index)`
Converts a stack value to a string.

**Parameters:**
- `J`: Interpreter state
- `index`: Stack index

**Returns:** Pointer to converted string

**Example:**
```c
const char *str = js_tostring(J, 1);
```

#### `int js_toint32(js_State *J, int index)`
Converts a stack value to a 32-bit integer.

**Parameters:**
- `J`: Interpreter state
- `index`: Stack index

**Returns:** Converted integer value

**Example:**
```c
int num = js_toint32(J, 1);
```

### Function Registration

#### `void js_newglobalfunc(js_State *J, js_CFunction func, const char *name, int length)`
Registers a C function as a global JavaScript function.

**Parameters:**
- `J`: Interpreter state
- `func`: C function pointer
- `name`: Name of the JavaScript function
- `length`: Number of parameters

**Example:**
```c
int my_function(js_State *J) {
    int a = js_toint32(J, 1);
    int b = js_toint32(J, 2);
    js_pushnumber(J, a + b);
    return 1;
}

js_newglobalfunc(J, my_function, "add", 2);
```

### Object and Array Operations

#### `js_Object *js_newobject(js_State *J)`
Creates a new JavaScript object.

**Parameters:**
- `J`: Interpreter state

**Returns:** Pointer to new object

**Example:**
```c
js_Object *obj = js_newobject(J);
```

#### `js_Array *js_newarray(js_State *J, int capacity)`
Creates a new JavaScript array.

**Parameters:**
- `J`: Interpreter state
- `capacity`: Initial array capacity

**Returns:** Pointer to new array

**Example:**
```c
js_Array *arr = js_newarray(J, 10);
```

### Property Access

#### `void js_getproperty(js_State *J, int index, const char *name)`
Retrieves an object property and pushes it onto the stack.

**Parameters:**
- `J`: Interpreter state
- `index`: Object stack index
- `name`: Property name

**Example:**
```c
js_getproperty(J, 1, "length");
```

#### `void js_setproperty(js_State *J, int index, const char *name)`
Sets an object property from the top of the stack.

**Parameters:**
- `J`: Interpreter state
- `index`: Object stack index
- `name`: Property name

**Example:**
```c
js_pushnumber(J, 42);
js_setproperty(J, 1, "value");
```

## Standard Library Functions

### Console Output

#### `print(value)`
Outputs a value to console.

**JavaScript Example:**
```javascript
print("Hello, World!");
print(42);
```

#### `printf(format, ...)`
Formatted output similar to C printf.

**JavaScript Example:**
```javascript
printf("Value: %d, Name: %s\n", 42, "test");
```

### Type Checking

#### `typeof(value)`
Returns the type of a value.

**JavaScript Example:**
```javascript
typeof(42);        // "number"
typeof("hello");   // "string"
typeof(true);      // "boolean"
```

### Global Functions

#### `parseInt(string, radix)`
Parses a string and returns an integer.

**JavaScript Example:**
```javascript
parseInt("42");      // 42
parseInt("1010", 2); // 10 (binary)
```

#### `parseFloat(string)`
Parses a string and returns a floating-point number.

**JavaScript Example:**
```javascript
parseFloat("3.14"); // 3.14
```

## Error Handling

#### `void js_seterror(js_State *J, const char *message)`
Sets an error message in the interpreter state.

**Parameters:**
- `J`: Interpreter state
- `message`: Error message string

**Example:**
```c
js_seterror(J, "Custom error message");
```

#### `const char *js_geterror(js_State *J)`
Retrieves the current error message.

**Parameters:**
- `J`: Interpreter state

**Returns:** Error message string, or NULL if no error

**Example:**
```c
const char *error = js_geterror(J);
if (error) {
    printf("Error: %s\n", error);
}
```

## Complete Example

```c
#include <stdio.h>
#include "js/jsapi.h"

int native_multiply(js_State *J) {
    int a = js_toint32(J, 1);
    int b = js_toint32(J, 2);
    js_pushnumber(J, a * b);
    return 1;
}

int main(void) {
    // Create interpreter state
    js_State *J = js_newstate(NULL, NULL, 0);
    
    // Register custom function
    js_newglobalfunc(J, native_multiply, "multiply", 2);
    
    // Execute JavaScript
    js_dostring(J, "var result = multiply(6, 7); print(result);");
    
    // Clean up
    js_freestate(J);
    return 0;
}
```

## Conclusion
This API reference provides the essential functions for embedding and extending the Termina-js JavaScript Interpreter. For more advanced usage and examples, refer to the source code and examples directory.