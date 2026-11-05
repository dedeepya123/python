# 05 - What Actually Happens During a Function Call?

## Prerequisite

From previous notes:

```text
Functions are objects.

Function names are variables pointing to function objects.

Functions are instances of the function class.
```

Natural Question:

> What happens when we call a function?

Example:

```python
def add(a, b):
    c = a + b
    return c

result = add(10, 20)
```

To understand function calls, we need to separate two concepts:

```text
1. Function Object (Persistent)
2. Function Execution (Temporary)
```

---

# Part 1: Function Object

Consider:

```python
def add(a, b):
    return a + b
```

Python creates a function object:

```text
add
 │
 ▼
Function Object
```

This object lives in memory even when it is not being executed.

Mental Model:

```text
Function Object

├── Code
├── __name__
├── __doc__
├── __defaults__
├── __annotations__
├── __globals__
└── __call__()
```

The function object is persistent.

It can be called many times.

---

# Function Object Lives Independently

Example:

```python
def add(a, b):
    return a + b
```

Even before calling:

```python
add(10, 20)
```

the function object already exists.

Mental Model:

```text
Heap

add Function Object
```

No execution is happening yet.

Only the function object exists.

---

# Part 2: Function Execution

When Python encounters:

```python
add(10, 20)
```

it does NOT create another function object.

Instead, Python creates an execution context called a:

```text
Stack Frame
```

---

# What is a Stack Frame?

A stack frame stores information needed while a function is executing.

Example:

```python
def add(a, b):
    c = a + b
    return c
```

Call:

```python
add(10, 20)
```

Python creates:

```text
Frame(add)

a = 10
b = 20
c = ?
```

After:

```python
c = a + b
```

the frame becomes:

```text
Frame(add)

a = 10
b = 20
c = 30
```

---

# Function Object vs Stack Frame

This distinction is critical.

## Function Object

Stores:

```text
Code
Function Metadata
Defaults
Annotations
Name
```

Lives for a long time.

Persistent.

---

## Stack Frame

Stores:

```text
Arguments
Local Variables
Execution State
Temporary Values
```

Lives only during the function call.

Temporary.

---

# Mental Model

Before Call

```text
Heap
====

Function Object (add)


Stack
=====

Main Frame
```

---

During Call

```python
add(10, 20)
```

```text
Heap
====

Function Object (add)


Stack
=====

Frame(add)

a = 10
b = 20
c = 30


Main Frame
```

---

After Return

```text
Heap
====

Function Object (add)


Stack
=====

Main Frame
```

The stack frame disappears.

The function object remains.

---

# Important Clarification

A common misconception is:

```text
Function Object lives on heap

Metadata lives on stack
```

Not exactly.

A better model is:

```text
Function Object
    contains metadata

Stack Frame
    contains execution information
```

---

# What Does the Function Object Store?

Example:

```python
def add(a, b):
    return a + b
```

Conceptually:

```text
Function Object

__name__       -> "add"
__defaults__   -> ...
__annotations__-> ...
__code__       -> compiled function code
```

These are part of the function object.

Not part of the stack frame.

---

# What Does the Stack Frame Store?

During:

```python
add(10, 20)
```

the frame stores:

```text
a = 10
b = 20
c = 30
```

and other execution information.

Examples:

```text
Arguments
Local Variables
Current Instruction
Return Information
Temporary Results
```

---

# Function Call Lifecycle

Whenever Python sees:

```python
add(10, 20)
```

you should imagine:

```text
1. Locate Function Object

2. Create Stack Frame

3. Bind Arguments

       a = 10
       b = 20

4. Execute Function Code

5. Produce Return Value

6. Destroy Stack Frame
```

---

# Why Can the Same Function Be Called Many Times?

Because:

```text
Function Object
```

remains alive.

Only:

```text
Stack Frames
```

are repeatedly created and destroyed.

Example:

```python
add(1, 2)
add(3, 4)
add(5, 6)
```

Mental Model:

```text
One Function Object

Many Temporary Stack Frames
```

---

# Multiple Calls Create Multiple Frames

Example:

```python
def add(a, b):
    return a + b
```

Call:

```python
add(1, 2)
add(3, 4)
```

Mental Model:

```text
Call #1

Create Frame
Execute
Destroy Frame

Call #2

Create Frame
Execute
Destroy Frame
```

The same function object is reused each time.

---

# Recursive Functions

Example:

```python
def fact(n):
    if n == 1:
        return 1

    return n * fact(n - 1)
```

Call:

```python
fact(3)
```

creates multiple stack frames.

Mental Model:

```text
Stack

Frame fact(3)

Frame fact(2)

Frame fact(1)
```

Each call gets its own frame.

All frames use the same function object.

---

# Example Walkthrough

```python
def greet(name):
    message = "Hello " + name
    return message

greet("Dedeepya")
```

Step 1

Locate function object:

```text
greet Function Object
```

---

Step 2

Create frame:

```text
Frame(greet)

name = "Dedeepya"
```

---

Step 3

Execute:

```python
message = "Hello Dedeepya"
```

Frame becomes:

```text
Frame(greet)

name = "Dedeepya"
message = "Hello Dedeepya"
```

---

Step 4

Return value:

```python
"Hello Dedeepya"
```

---

Step 5

Destroy frame:

```text
Frame removed
```

Function object remains.

---

# Quick Summary

```text
Function Object
    Persistent
    Stores code and metadata

Stack Frame
    Temporary
    Stores execution information

Calling a function does NOT create a new function object.

Calling a function creates a stack frame.

The stack frame exists only during execution.

When the function returns:
    Stack frame is destroyed.

The function object remains.
```

---

# Golden Mental Models

### Mental Model 1

```text
A function consists of:

1. Function Object
2. Stack Frame
```

---

### Mental Model 2

```text
Function Object

Stores:
    code
    metadata
    defaults
    annotations
```

---

### Mental Model 3

```text
Stack Frame

Stores:
    arguments
    local variables
    execution state
```

---

### Mental Model 4

```text
Calling a function:

Locate Function Object
        ↓
Create Frame
        ↓
Bind Arguments
        ↓
Execute Code
        ↓
Return Value
        ↓
Destroy Frame
```

---

### Mental Model 5

```text
One Function Object

Can create

Many Temporary Stack Frames
```

---

# Final Picture

```text
Function Name
       │
       ▼
Function Object
       │
       ├── Code
       ├── __name__
       ├── __defaults__
       ├── __annotations__
       └── __call__()
       │
       ▼
Function Class
       │
       ▼
object
```

Calling:

```python
func(...)
```

creates:

```text
Function Object
       │
       ▼
Create Stack Frame
       │
       ▼
Execute
       │
       ▼
Return
       │
       ▼
Destroy Frame
```

## Golden Rule

```text
Function objects hold the blueprint.

Stack frames hold the execution.

Calling a function creates a stack frame,
not another function object.
```
