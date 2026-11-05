# 07 - Scope and Name Resolution (LEGB)

## Prerequisite

From previous notes:

```text
Function calls create stack frames.

Parameters become local variables inside the frame.

Variables are names that reference objects.
```

Natural Question:

> When Python encounters a variable name, how does it find the corresponding object?

Example:

```python
x = 10

def fn():
    print(x)
```

How does Python know where `x` is?

The answer is:

```text
Name Resolution
```

Python searches through scopes.

---

# What is a Scope?

A scope is simply:

```text
A region where names can be found.
```

Think of a scope as a dictionary:

```text
name -> object
```

Example:

```python
x = 10
```

Conceptually:

```text
{
    "x": 10
}
```

When Python sees:

```python
print(x)
```

it asks:

```text
Where can I find the name x?
```

---

# The LEGB Rule

Python searches for names in a fixed order:

```text
L → Local
E → Enclosing
G → Global
B → Builtins
```

This is called:

```text
LEGB Name Resolution
```

Search Order:

```text
Local
  ↓
Enclosing
  ↓
Global
  ↓
Builtins
```

The first match wins.

Python stops searching immediately.

---

# 1. Local Scope (L)

Local scope is the current function's stack frame.

Example:

```python
def fn():
    x = 10

    print(x)

fn()
```

Stack Frame:

```text
Frame(fn)

x = 10
```

When Python sees:

```python
print(x)
```

Search:

```text
Local?
Yes.
```

Output:

```python
10
```

---

## Mental Model

```text
Frame(fn)

x = 10
```

Python looks for:

```text
x
```

Search:

```text
Local
 ↑
Found
```

Stop.

---

# 2. Global Scope (G)

Global scope contains names defined at the module level.

Example:

```python
x = 100

def fn():
    print(x)

fn()
```

Search:

```text
Local?
No

Global?
Yes
```

Output:

```python
100
```

---

## Mental Model

```text
Global Scope

x = 100
```

Function Frame:

```text
Frame(fn)

(no local x)
```

Search Path:

```text
Local
 ↓
Global
 ↑
Found
```

---

# Local Variables Shadow Global Variables

Example:

```python
x = 100

def fn():
    x = 50

    print(x)

fn()
```

Python finds:

```text
Local x
```

before

```text
Global x
```

Output:

```python
50
```

---

## Mental Model

```text
Global

x = 100
```

```text
Frame(fn)

x = 50
```

Search:

```text
Local
 ↑
Found
```

Global is never checked.

---

# 3. Enclosing Scope (E)

Enclosing scopes appear in nested functions.

Example:

```python
def outer():

    x = 10

    def inner():
        print(x)

    inner()

outer()
```

Question:

```text
Where does inner find x?
```

Search:

```text
Local?
No

Enclosing?
Yes
```

Output:

```python
10
```

---

## Mental Model

```text
Frame(outer)

x = 10

      │
      ▼

Frame(inner)

print(x)
```

Search:

```text
Local?
No

Enclosing?
Found
```

---

# Nested Scope Example

```python
def outer():

    outer_var = 100

    def inner():

        print(outer_var)

    inner()
```

Search:

```text
Local?
No

Enclosing?
Yes
```

The enclosing function provides the variable.

---

# 4. Builtins Scope (B)

Suppose Python cannot find a name in:

```text
Local
Enclosing
Global
```

Then it checks:

```text
Builtins
```

Example:

```python
print("hello")
```

Question:

```text
Where is print defined?
```

Not local.

Not enclosing.

Not global.

Python finds it in:

```text
Builtins
```

---

## Common Builtins

```python
print
len
range
list
dict
set
tuple
int
float
str
```

These are automatically available.

---

## Mental Model

```text
Builtins

print
len
range
list
dict
int
...
```

Python automatically searches here last.

---

# Complete LEGB Example

```python
x = "global"

def outer():

    x = "enclosing"

    def inner():

        x = "local"

        print(x)

    inner()

outer()
```

Search:

```text
Local?
Yes
```

Output:

```python
local
```

---

# Remove Local

```python
x = "global"

def outer():

    x = "enclosing"

    def inner():

        print(x)

    inner()

outer()
```

Search:

```text
Local?
No

Enclosing?
Yes
```

Output:

```python
enclosing
```

---

# Remove Enclosing

```python
x = "global"

def outer():

    def inner():

        print(x)

    inner()

outer()
```

Search:

```text
Local?
No

Enclosing?
No

Global?
Yes
```

Output:

```python
global
```

---

# Assignment Creates Local Variables

A very important rule.

Example:

```python
x = 100

def fn():

    x = 50

    print(x)
```

The line:

```python
x = 50
```

creates a new local variable.

It does NOT modify the global variable.

After:

```python
fn()
```

Global remains:

```python
x = 100
```

---

## Mental Model

Without assignment:

```text
Read global
```

With assignment:

```text
Create local
```

---

# The global Keyword

What if we really want to modify a global variable?

Example:

```python
x = 100

def fn():
    global x

    x = 50

fn()

print(x)
```

Output:

```python
50
```

---

## Mental Model

Without:

```python
global x
```

Python creates:

```text
Local x
```

With:

```python
global x
```

Python uses:

```text
Global x
```

instead.

---

# The nonlocal Keyword

Nested functions introduce another keyword.

Example:

```python
def outer():

    x = 10

    def inner():

        nonlocal x

        x = 20

    inner()

    print(x)

outer()
```

Output:

```python
20
```

---

## Why?

Without:

```python
nonlocal
```

Python creates:

```text
Local x inside inner()
```

With:

```python
nonlocal x
```

Python modifies:

```text
Enclosing x
```

---

## Mental Model

```text
Inner Function

        │
        ▼

nonlocal x

        │
        ▼

Use enclosing scope variable
```

---

# Scope Inspection

## locals()

Shows the current local namespace.

Example:

```python
def fn(a, b):

    c = a + b

    print(locals())

fn(1, 2)
```

Output:

```python
{
    'a': 1,
    'b': 2,
    'c': 3
}
```

Think:

```text
Current Stack Frame Namespace
```

---

## globals()

Shows the module's global namespace.

Example:

```python
print(globals())
```

Think:

```text
Module-Level Namespace
```

---

# Why LEGB Matters

Consider:

```python
def outer():

    x = 10

    def inner():
        print(x)

    return inner
```

Question:

```text
The outer function returns.

Its frame is destroyed.

How can inner still access x later?
```

This question leads directly to:

```text
Closures
```

Closures are built on top of LEGB.

Without understanding LEGB, closures seem magical.

---

# Quick Summary

```text
Scope
    Region where names can be found.

Name Resolution
    Process of finding a name's value.

Python uses LEGB:

L → Local
E → Enclosing
G → Global
B → Builtins
```

---

# Golden Mental Models

## Mental Model 1

```text
Variables live in namespaces.

A namespace is essentially:

name -> object
```

---

## Mental Model 2

```text
A scope is a namespace that Python searches.
```

---

## Mental Model 3

When Python sees:

```python
print(x)
```

it searches:

```text
Local
 ↓
Enclosing
 ↓
Global
 ↓
Builtins
```

---

## Mental Model 4

```text
Nearest scope wins.
```

Priority:

```text
Local
 >
Enclosing
 >
Global
 >
Builtins
```

---

## Mental Model 5

```text
Assignment inside a function
creates a local variable
unless instructed otherwise.
```

Use:

```python
global
```

or

```python
nonlocal
```

to change that behavior.

---

# Final Picture

```text
Builtins
    │
    ▼

Global Scope
    │
    ▼

Outer Function Scope
    │
    ▼

Current Function Scope
```

When Python searches for a name:

```text
Local
 ↓
Enclosing
 ↓
Global
 ↓
Builtins
```

Python stops at the first match.

---

# Golden Rule

```text
Names are resolved using LEGB.

Local
Enclosing
Global
Builtins

The nearest matching scope always wins.
```
