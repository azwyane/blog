---
title: "Garbage Collection in Python"
datePublished: Wed Jun 21 2023 05:56:12 GMT+0000 (Coordinated Universal Time)
cuid: clj5awwod000x09jpf28n7nig
slug: garbage-collection-in-python
tags: python

---

Garbage collection but before that let's do a quick dictionary work:

```python
schedule = {
            "morning":"apple",
            "arvo":"orange",
            "evening":"apple"
            }
```

The above dictionary shows the dietary schedule. It's interesting to see I have some fruits repeating for the day. If I had to find how many times a single fruit I have to eat, I would have to count how many times a fruit has been mentioned in my above schedule.

```python
from collections import Counter
schedule = {
            "morning":"apple",
            "arvo":"orange",
            "evening":"apple"
            }

fruit_count = Counter(schedule.values())
print(fruit_count) #Counter({'apple': 2, 'orange': 1})
```

The above shows fruit counts in my schedule. The above result is "Reference Count" (a word used to describe the above result) of fruits in our schedule, which shows the number of times our fruit has been referenced.

#### Let's talk global.

So, until now you know what reference count is.

Do you know that Python has various objects created at runtime that are readily available to you?

They are imported from the builtins module. (Out of scope for now)

Why I brought this topic here is to understand the scope, that is every object has a scope of existence, builtins provided objects have a scope of existence until the program exits, and global objects have scope within the program itself. You can access the global objects via globals().

```python
globals()
{
  '__name__': '__main__',
  '__builtins__': <module 'builtins' (built-in)>,
  '__doc__': None,
  '__file__': '<stdin>',
  '__package__': None,
  '__loader__': <class '_frozen_importlib.BuiltinImporter'>,
  '__spec__': None,
  '__annotations__': {},
}
```

If you create a new object in global scope then it can be viewed using globals.

```python
egg = 20

print(globals()) 
{
  '__name__': '__main__',
  'egg': 20,
  '__builtins__': <module 'builtins' (built-in)>,
  '__doc__': None,
  '__file__': '<stdin>',
  '__package__': None,
  '__loader__': <class '_frozen_importlib.BuiltinImporter'>,
  '__spec__': None,
  '__annotations__': {},
}
```

This code will first create an integer object called `egg` and assign it the value of 20. Then, it will print the globals dictionary. The globals dictionary contains all of the global variables that are defined in the current scope. In this case, the globals dictionary will contain the variable `egg`.

The key `egg` is associated with the value 20. This means that the integer object `egg` is still in use.

```python
egg = 20
ball = egg
print(globals()) 
{
  '__name__': '__main__',
  'egg': 20,
  'ball':20,
  '__builtins__': <module 'builtins' (built-in)>,
  '__doc__': None,
  '__file__': '<stdin>',
  '__package__': None,
  '__loader__': <class '_frozen_importlib.BuiltinImporter'>,
  '__spec__': None,
  '__annotations__': {},
}
```

We see that 20 has been referenced by both var egg and ball, and using the initial logic of reference count we have reference count 2 for object 20 (in a real scenario reference count for 20 is not exactly 2, python has already cached and referenced so count may be different).

For every new reference to an object, the garbage collector increments the reference count and decrements whenever a reference to the object is deleted.

That is

```python
del egg
print(globals())
{
  '__name__': '__main__',
  'ball':20,
  '__builtins__': <module 'builtins' (built-in)>,
  '__doc__': None,
  '__file__': '<stdin>',
  '__package__': None,
  '__loader__': <class '_frozen_importlib.BuiltinImporter'>,
  '__spec__': None,
  '__annotations__': {},
}
```

Here, `del egg` deletes the reference to object 20, and `gc` decrements the reference count to object 20.

(You know now that `del object` does not delete the object but the reference to it)

#### So do we have objects with 0 reference count? what do we do with it?

Yes, we do, and that is what garbage collector uses to track objects and finally delete them.

The reference count dictionary in Python is not accessible directly but can be using `gc` module itself. However, you can access it indirectly by using the `sys.getrefcount()` function.

For nerds, the following code will print the reference count of the object `egg`:

```python
import sys

class Oval:
    pass

egg = Oval()

print(sys.getrefcount(egg)) #2
```

This code will print the number of references that are currently pointing to the object `egg`.

#### How does `gc` handle when an object's reference count reaches 0?

The garbage collector knows that the object is no longer in use and can be deleted if the reference count is 0. But, given that the object, if is still referenced in the globals or builtins dictionaries`gc` will not directly delete it.

Python's garbage collector is a powerful tool for managing memory. It automatically deletes objects that are no longer in use, which helps to keep memory usage low. This frees programmers from having to worry about memory management, which can be a complex and error-prone task.