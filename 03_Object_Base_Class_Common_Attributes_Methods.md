# 03 - Common Attributes and Methods Inherited from object

## Prerequisite

From previous notes:

```text
Every object is an instance of some class.

Most classes ultimately inherit from object.

Therefore every object gets some common behavior from object.
```

Natural question:

> What common attributes and methods does every Python object have?

The answer lies in the root base class:

```python
object
```

---

# Inspecting the object Class

We can ask Python:

```python
print(dir(object))
```

Output (truncated):

```python
[
    '__class__',
    '__delattr__',
    '__dir__',
    '__eq__',
    '__format__',
    '__ge__',
    '__getattribute__',
    '__gt__',
    '__hash__',
    '__init__',
    '__le__',
    '__lt__',
    '__ne__',
    '__new__',
    '__repr__',
    '__setattr__',
    '__sizeof__',
    '__str__'
]
```

These special methods are often called:

```text
Magic Methods
Special Methods
Dunder Methods (Double Underscore Methods)
```

Example:

```python
__str__
__repr__
__eq__
__getattribute__
```

---

# 1. __class__

Returns the class of an object.

```python
x = 10

print(x.__class__)
```

Output:

```python
<class 'int'>
```

Another example:

```python
s = "hello"

print(s.__class__)
```

Output:

```python
<class 'str'>
```

Mental Model:

```text
Object
  │
  └── Which class created you?

Answer:
int
str
list
Tensor
Person
...
```

---

# 2. __str__()

Provides a human-readable string representation.

```python
x = 10

print(x.__str__())
```

Output:

```python
10
```

When Python executes:

```python
print(x)
```

it internally calls:

```python
x.__str__()
```

Mental Model:

```text
print(obj)
     │
     ▼
obj.__str__()
```

---

# 3. __repr__()

Provides a developer-oriented representation.

```python
x = 10

print(repr(x))
```

Internally:

```python
x.__repr__()
```

Example:

```python
lst = [1, 2, 3]

print(repr(lst))
```

Output:

```python
[1, 2, 3]
```

Mental Model:

```text
repr(obj)
     │
     ▼
obj.__repr__()
```

---

# 4. __eq__()

Defines equality behavior.

Example:

```python
a = 10
b = 10

print(a == b)
```

Internally:

```python
a.__eq__(b)
```

Result:

```python
True
```

Mental Model:

```text
a == b
   │
   ▼
a.__eq__(b)
```

---

# 5. __hash__()

Used when objects become keys in dictionaries or members of sets.

```python
x = 10

print(hash(x))
```

Internally:

```python
x.__hash__()
```

Mental Model:

```text
hash(obj)
     │
     ▼
obj.__hash__()
```

Used heavily by:

```python
dict
set
```

---

# 6. __getattribute__()

One of the most important methods in Python.

Whenever:

```python
obj.attr
```

is executed,

Python internally does approximately:

```python
obj.__getattribute__("attr")
```

Example:

```python
s = "hello"

s.upper
```

Internally:

```python
s.__getattribute__("upper")
```

Mental Model:

```text
obj.attr
   │
   ▼
__getattribute__("attr")
```

Without this method:

```python
tensor.shape
model.forward
list.append
str.upper
```

would not work.

---

# 7. __setattr__()

Called whenever an attribute is assigned.

Example:

```python
p.age = 25
```

Internally:

```python
p.__setattr__("age", 25)
```

Mental Model:

```text
obj.attr = value
      │
      ▼
__setattr__(attr, value)
```

---

# 8. __delattr__()

Called whenever an attribute is deleted.

Example:

```python
del p.age
```

Internally:

```python
p.__delattr__("age")
```

Mental Model:

```text
del obj.attr
      │
      ▼
__delattr__(attr)
```

---

# 9. __dir__()

Used by the built-in function:

```python
dir(obj)
```

Example:

```python
x = 10

print(dir(x))
```

Internally:

```python
x.__dir__()
```

Mental Model:

```text
dir(obj)
    │
    ▼
obj.__dir__()
```

---

# Why These Methods Matter

Because almost every Python object inherits from:

```python
object
```

all objects automatically gain the ability to:

```text
Know their class
Display themselves
Compare themselves
Store attributes
Delete attributes
Look up attributes
Participate in dictionaries and sets
```

---

# Real PyTorch Examples

Tensor Shape

```python
tensor.shape
```

Internally:

```python
tensor.__getattribute__("shape")
```

---

Module Forward

```python
model.forward
```

Internally:

```python
model.__getattribute__("forward")
```

---

Training Flag

```python
model.training
```

Internally:

```python
model.__getattribute__("training")
```

PyTorch does not bypass Python's object model.

It builds on top of it.

---

# The Most Important Method

Of all methods inherited from `object`:

```python
__getattribute__()
```

is arguably the most important.

Every attribute lookup passes through it.

Example:

```python
obj.x
obj.weight
obj.bias
obj.forward
obj.shape
```

All become approximately:

```python
obj.__getattribute__(...)
```

Mental Model:

```text
Attribute Access
      │
      ▼
__getattribute__()
      │
      ▼
Search Object
      │
      ▼
Search Class
      │
      ▼
Search Parent Classes
```

This mechanism forms the foundation of:

```text
Object Attributes
Methods
Inheritance
self
PyTorch Modules
Tensor Properties
Method Calls
```

---

# Quick Summary

```text
object is the root base class of most classes.

Common methods inherited from object:

__class__
    Returns the object's class.

__str__()
    Human-readable representation.

__repr__()
    Developer representation.

__eq__()
    Implements ==.

__hash__()
    Used by dict and set.

__getattribute__()
    Handles attribute lookup.

__setattr__()
    Handles attribute assignment.

__delattr__()
    Handles attribute deletion.

__dir__()
    Used by dir().
```

---

# Golden Mental Models

### Mental Model 1

```text
Every object inherits common behavior from object.
```

---

### Mental Model 2

```text
Attribute access is not magic.

obj.attr

becomes approximately

obj.__getattribute__("attr")
```

---

### Mental Model 3

```text
Most Python features are implemented through
special methods.
```

Examples:

```text
print(obj)      -> __str__()

repr(obj)       -> __repr__()

obj1 == obj2    -> __eq__()

hash(obj)       -> __hash__()

obj.attr        -> __getattribute__()

obj.attr = x    -> __setattr__()
```

---

# Golden Rule

```text
The object class provides the fundamental behavior
shared by nearly every Python object.

The most important mechanism is attribute lookup,
which is implemented through __getattribute__().
```
