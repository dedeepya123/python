# 06 - Parameters and Argument Binding

## Prerequisite

From previous notes:

```text
Functions are objects.

Calling a function creates a stack frame.

The stack frame holds:
    - Arguments
    - Local Variables
    - Execution State
```

Natural Question:

> When a function is called, how do parameters get their values?

Example:

```python
def add(a, b):
    return a + b

add(10, 20)
```

How does:

```text
a = 10
b = 20
```

happen?

The answer is:

```text
Argument Binding
```

---

# Parameters vs Arguments

A very important distinction.

## Parameters

Parameters are the variable names in the function definition.

Example:

```python
def add(a, b):
    pass
```

Here:

```text
a → Parameter
b → Parameter
```

Mental Model:

```text
Empty Slots

a = ?
b = ?
```

Parameters are placeholders waiting for values.

---

## Arguments

Arguments are the actual values supplied during a function call.

Example:

```python
add(10, 20)
```

Here:

```text
10 → Argument
20 → Argument
```

Arguments are the values that fill the parameter slots.

---

# Basic Argument Binding

Example:

```python
def add(a, b):
    return a + b

add(10, 20)
```

Python creates a stack frame:

```text
Frame(add)
```

Then binds:

```text
a = 10
b = 20
```

Resulting frame:

```text
Frame(add)

a = 10
b = 20
```

Now `a` and `b` are ordinary local variables.

---

# Positional Arguments

Example:

```python
def introduce(name, age):
    print(name, age)

introduce("Dedeepya", 25)
```

Binding:

```text
name = "Dedeepya"
age = 25
```

Python matches by position.

Mental Model:

```text
Position 1 → name
Position 2 → age
```

Visualization:

```text
introduce(
    "Dedeepya",
    25
)

      │
      ▼

name = "Dedeepya"
age  = 25
```

---

# Positional Order Matters

Example:

```python
introduce(25, "Dedeepya")
```

Binding:

```text
name = 25
age  = "Dedeepya"
```

Python does not understand meaning.

It only binds based on position.

---

# Keyword Arguments

Example:

```python
introduce(
    age=25,
    name="Dedeepya"
)
```

Binding:

```text
name = "Dedeepya"
age = 25
```

Now order does not matter.

Python matches using parameter names.

Mental Model:

```text
name="Dedeepya"
      │
      ▼
Parameter: name

age=25
   │
   ▼
Parameter: age
```

---

# Mixing Positional and Keyword Arguments

Example:

```python
introduce(
    "Dedeepya",
    age=25
)
```

Binding:

```text
name = "Dedeepya"
age = 25
```

This is common and perfectly valid.

---

# Default Arguments

Example:

```python
def greet(name, msg="Hello"):
    print(msg, name)
```

Parameters:

```text
name → Required
msg → Has Default Value
```

Mental Model:

```text
msg
 │
 ▼
"Hello"
```

---

Call:

```python
greet("Dedeepya")
```

Binding:

```text
name = "Dedeepya"
msg  = "Hello"
```

The default value is used.

---

Call:

```python
greet("Dedeepya", "Hi")
```

Binding:

```text
name = "Dedeepya"
msg  = "Hi"
```

The provided argument overrides the default.

---

# Arbitrary Positional Arguments (*args)

Sometimes we don't know how many arguments will be passed.

Example:

```python
def add_all(*args):
    print(args)
```

Call:

```python
add_all(1, 2, 3, 4)
```

Output:

```python
(1, 2, 3, 4)
```

Type:

```python
print(type(args))
```

Output:

```python
<class 'tuple'>
```

Mental Model:

```text
Extra Positional Arguments

1
2
3
4

      │
      ▼

Packed Into

args = (1,2,3,4)
```

---

# Arbitrary Keyword Arguments (**kwargs)

Example:

```python
def fn(**kwargs):
    print(kwargs)
```

Call:

```python
fn(
    name="Dedeepya",
    age=25
)
```

Output:

```python
{
    "name": "Dedeepya",
    "age": 25
}
```

Type:

```python
print(type(kwargs))
```

Output:

```python
<class 'dict'>
```

Mental Model:

```text
name = "Dedeepya"
age = 25

       │
       ▼

kwargs

{
    "name": "Dedeepya",
    "age": 25
}
```

---

# Combining Everything

Example:

```python
def fn(a, b=10, *args, **kwargs):
    pass
```

Call:

```python
fn(
    1,
    2,
    3,
    4,
    x=5,
    y=6
)
```

Binding:

```text
a = 1

b = 2

args = (3, 4)

kwargs = {
    "x": 5,
    "y": 6
}
```

Resulting frame:

```text
Frame(fn)

a = 1

b = 2

args = (3, 4)

kwargs = {
    "x": 5,
    "y": 6
}
```

---

# Why *args and **kwargs Are Useful

Many APIs do not know in advance exactly what arguments they will receive.

Example:

```python
def wrapper(*args, **kwargs):
    return fn(*args, **kwargs)
```

Examples from real-world Python:

```python
PyTorch:
    forward(*args, **kwargs)

Decorators:
    wrapper(*args, **kwargs)

Logging Libraries:
    log(*args, **kwargs)
```

These patterns allow flexible argument passing.

---

# What Actually Happens During a Function Call?

Consider:

```python
fn(1, 2, 3, x=4)
```

Python roughly performs:

```text
1. Locate Function Object

2. Create Stack Frame

3. Match Arguments To Parameters

4. Store Bound Values In Frame

5. Execute Function Code

6. Return Value

7. Destroy Frame
```

---

# Most Important Insight

Parameters are special only during argument binding.

After binding:

```python
def add(a, b):
    print(a)
    print(b)
```

Frame becomes:

```text
a = 10
b = 20
```

From that point forward:

```text
a and b are ordinary local variables.
```

They behave exactly like variables created inside the function body.

---

# Quick Summary

```text
Parameter
    Placeholder variable in function definition.

Argument
    Value supplied during a function call.

Argument Binding
    Process of assigning arguments to parameters.

Positional Arguments
    Bound by position.

Keyword Arguments
    Bound by parameter name.

Default Arguments
    Used when no value is supplied.

*args
    Collects extra positional arguments into a tuple.

**kwargs
    Collects extra keyword arguments into a dictionary.
```

---

# Golden Mental Models

### Mental Model 1

```text
Parameters are empty slots.
```

Example:

```text
a = ?
b = ?
```

---

### Mental Model 2

```text
Arguments fill parameter slots.
```

Example:

```text
a = 10
b = 20
```

---

### Mental Model 3

```text
Function Call

fn(...)

creates a stack frame

and performs argument binding.
```

---

### Mental Model 4

```text
After binding,

parameters become ordinary
local variables inside the frame.
```

---

### Mental Model 5

```text
*args
    → tuple of extra positional arguments

**kwargs
    → dictionary of extra keyword arguments
```

---

# Final Picture

```text
Function Object
        │
        ▼

fn(1, 2, 3, x=4)

        │
        ▼

Create Frame

a = 1
b = 2

args = (3,)

kwargs = {
    "x": 4
}

        │
        ▼

Execute Function
```

## Golden Rule

```text
A function call is fundamentally
an argument-binding operation.

The stack frame is populated by
binding arguments to parameters,
after which those parameters become
ordinary local variables.
```
