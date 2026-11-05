# 08 - Closures: Functions That Remember State

## Prerequisite

From previous lessons:

```text
Functions are objects.

Function calls create stack frames.

When a function returns,
its stack frame is destroyed.

Python resolves names using:

LEGB

Local
Enclosing
Global
Builtins
```

Natural Question:

> If an enclosing function's frame is destroyed,
> how can an inner function still access variables
> from that enclosing scope?

Example:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner
```

After:

```python
outer()
```

the frame of `outer()` should be destroyed.

So how does `inner()` still access `x`?

The answer is:

```text
Closure
```

---

# The Problem

Consider:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner
```

Usage:

```python
fn = outer()
```

Then:

```python
fn()
```

Output:

```python
10
```

Question:

```text
Where did x come from?

The outer frame should already be gone.
```

---

# Step-by-Step Execution

## Step 1: Create outer Function Object

When Python reads:

```python
def outer():
```

it creates:

```text
outer
 │
 ▼
Function Object
```

Only the outer function object exists at this point.

---

## Step 2: Call outer()

```python
outer()
```

Python creates:

```text
Frame(outer)

x = 10
```

Mental Model:

```text
Frame(outer)

x ─────► 10
```

---

## Step 3: Reach inner Definition

Inside `outer()`:

```python
def inner():
    print(x)
```

Python now creates:

```text
inner Function Object
```

Important:

```text
inner is NOT created when outer is defined.

inner is created when outer executes.
```

---

## Step 4: Python Analyzes inner

Python examines:

```python
print(x)
```

and notices:

```text
x is not local to inner.

x comes from an enclosing scope.
```

Python now decides:

```text
inner needs access to x later.
```

---

## Step 5: Create Closure Metadata

Python attaches closure information to the function object.

Conceptually:

```text
inner Function Object

Code:
    print(x)

Closure:
    x
```

The inner function now remembers that it depends on `x`.

---

## Step 6: Return inner

```python
return inner
```

Now:

```python
fn = outer()
```

becomes:

```text
fn
 │
 ▼
inner Function Object
```

---

## Step 7: outer() Finishes

Normally:

```text
Frame(outer)
```

would be destroyed.

However:

```text
inner still needs x
```

Therefore Python preserves the captured value.

---

## Step 8: Call inner()

```python
fn()
```

Output:

```python
10
```

Even though:

```text
Frame(outer)
```

is gone.

The captured variable survives through the closure.

---

# What Exactly Is a Closure?

A common misconception:

```text
Closure = Inner Function
```

Not quite.

A better definition:

```text
Closure =
    Function Object
    +
    Captured Variables
```

---

## Normal Function

```text
Function Object

Code
Metadata
```

---

## Closure

```text
Function Object

Code
Metadata
Captured Variables
```

Think:

```text
Closure

=

Function

+

Remembered Environment
```

---

# First Mental Model

```text
A closure is a function that remembers
variables from its enclosing scope.
```

---

# The Role of LEGB

Recall:

```text
Local
Enclosing
Global
Builtins
```

Example:

```python
def outer():

    x = 10

    def inner():
        print(x)
```

When Python analyzes:

```python
x
```

it sees:

```text
Local?
No

Enclosing?
Yes
```

Because the variable comes from:

```text
Enclosing Scope
```

Python creates closure metadata.

---

# Function Object Before Closure

Conceptually:

```text
inner Function Object

Code
Metadata
```

---

# Function Object After Closure

Conceptually:

```text
inner Function Object

Code
Metadata

Closure:
    x = 10
```

The function now carries remembered state.

---

# Important Distinction

When `inner()` executes later, Python does NOT search for `x` by finding the old outer frame.

The outer frame is gone.

Instead:

```text
inner already knows
that x belongs to its closure.
```

---

# What Actually Gets Stored?

Python does not store:

```text
Outer Frame
```

inside the closure.

Instead it stores special objects called:

```text
Cell Objects
```

---

# Cell Mental Model

Inside:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner
```

Conceptually:

```text
Cell
 │
 ▼
10
```

The closure stores:

```text
Reference To Cell
```

not a copy of the entire frame.

---

# More Accurate Internal Picture

```text
inner Function Object

Closure

    │
    ▼

Cell(x)

    │
    ▼

10
```

This is why captured variables continue to exist.

---

# Functions Can Remember State

Example:

```python
def power(exponent):

    def inner(x):
        return x ** exponent

    return inner
```

Usage:

```python
square = power(2)

cube = power(3)
```

---

## square

Mental Model:

```text
square

Function Object

Closure:
    exponent = 2
```

---

## cube

Mental Model:

```text
cube

Function Object

Closure:
    exponent = 3
```

---

Usage:

```python
square(5)
```

Output:

```python
25
```

because:

```text
Closure remembers exponent = 2
```

---

Usage:

```python
cube(5)
```

Output:

```python
125
```

because:

```text
Closure remembers exponent = 3
```

---

# Closures Preserve State

Example:

```python
def counter():

    count = 0

    def increment():
        nonlocal count

        count += 1

        return count

    return increment
```

Usage:

```python
c = counter()

c()
c()
c()
```

Output:

```python
1
2
3
```

Why?

Because:

```text
count survives inside the closure.
```

---

# Inspecting Closures

Example:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner

fn = outer()
```

Inspect:

```python
print(fn.__closure__)
```

Output:

```python
(<cell at ...>,)
```

Python is literally storing closure information.

---

Inspect captured value:

```python
print(fn.__closure__[0].cell_contents)
```

Output:

```python
10
```

---

# Closures Power Decorators

Consider:

```python
def decorator(fn):

    def wrapper():

        print("Before")

        fn()

        print("After")

    return wrapper
```

Question:

```text
How does wrapper remember fn?
```

Answer:

```text
Closure
```

Mental Model:

```text
wrapper Function

Closure:

fn ─────► Original Function
```

Without closures, decorators would not work.

---

# Function Factory Mental Model

Closures allow functions to create customized functions.

Example:

```python
def multiply_by(n):

    def inner(x):
        return x * n

    return inner
```

Usage:

```python
double = multiply_by(2)

triple = multiply_by(3)
```

Mental Model:

```text
multiply_by

Function Factory
```

Produces:

```text
Customized Functions
```

Each function remembers different state.

---

# Lifecycle Summary

Example:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner
```

Execution:

```text
Call outer()

        │
        ▼

Create Frame(outer)

        │
        ▼

x = 10

        │
        ▼

Create inner Function Object

        │
        ▼

Detect x comes from enclosing scope

        │
        ▼

Create Closure Metadata

        │
        ▼

Return inner

        │
        ▼

Destroy outer Frame

        │
        ▼

inner survives

        │
        ▼

inner() still accesses x
```

---

# Quick Summary

```text
Closure

A function together with the variables
it remembers from its enclosing scope.
```

---

```text
Normal Function

Code
Metadata
```

---

```text
Closure

Code
Metadata
Captured Variables
```

---

# Golden Mental Models

## Mental Model 1

```text
A closure is not just a function.

A closure is:

Function
+
Remembered Environment
```

---

## Mental Model 2

```text
The outer frame can disappear.

Captured variables survive.
```

---

## Mental Model 3

```text
Closures are created because of
the Enclosing part of LEGB.
```

---

## Mental Model 4

```text
Inner functions are created when
the outer function executes,
not when the outer function is defined.
```

---

## Mental Model 5

```text
Closures do not store
the entire outer frame.

Closures store references
to captured variables.
```

---

## Mental Model 6

```text
Captured variables live in
special cell objects.
```

---

# Final Picture

```text
outer()

Frame(outer)

x = 10
```

Creates:

```text
inner Function Object

Code:
    print(x)

Closure:
    Cell(x)
        │
        ▼
        10
```

Then:

```text
Frame(outer)
```

is destroyed.

But:

```text
inner Function Object

Closure:
    Cell(x)
        │
        ▼
        10
```

survives.

Therefore:

```python
inner()
```

can still access:

```python
x
```

even after the outer function has returned.

---

# Golden Rule

```text
A closure is a function object that carries
the variables it needs from its enclosing scope.

The outer frame may disappear,
but the captured variables remain alive
through closure cell objects.
```
